# Spring AOP와 @Transactional 자세한 설명 및 로그인 예시

---

## 1. Spring AOP란?

### ✦ 개념

AOP(관점 지향 프로그래밍, Aspect-Oriented Programming)는 **공통 기능(관심사, cross-cutting concern)**을 핵심 로직과 분리하여 관리할 수 있게 해주는 프로그래밍 패러다임입니다. 대표적인 공통 기능에는 **로깅, 트랜잭션, 보안, 예외처리** 등이 있습니다.

- **OOP(객체지향)**가 객체 단위로 구조화했다면
- **AOP**는 "관심사" 단위로 모듈화해서, 코드 중복을 줄이고, 유지보수를 쉽게 합니다.

### ✦ AOP 주요 용어

| 용어         | 설명                                         |
| ---------- | ------------------------------------------ |
| Aspect     | 부가 기능(공통 관심사, 예: 로깅, 트랜잭션)을 모듈화한 객체 또는 클래스 |
| Join Point | Aspect가 적용될 수 있는 지점(메서드 실행, 예외 발생 등)       |
| Advice     | 실제로 수행될 작업(로깅, 트랜잭션 시작/종료 등)               |
| Pointcut   | Advice가 적용될 Join Point의 집합(선택 규칙)          |
| Weaving    | 핵심 로직 코드와 Aspect 코드를 합치는 것                 |

### ✦ Spring에서의 AOP

- Spring은 **프록시(Proxy) 기반의 AOP**를 사용합니다.
- 주로 **메서드 실행 전/후**, **예외 발생 시** 등 다양한 시점에 부가 기능을 삽입합니다.

---

## 2. Spring AOP 사용 예시 – 로그인 로깅

### ✦ 예시 구조

- **핵심 로직**: 로그인 기능(AuthService)
- **부가 기능**: 로그인 시도 시 로그 출력(LoginLoggingAspect)

#### [1] 핵심 로직 (Service)

```java
@Service
public class AuthService {
    public boolean login(String username, String password) {
        System.out.println("[SERVICE] 로그인 시도 - username=" + username);
        // 실제론 DB 조회 등 인증 로직
        if ("admin".equals(username) && "1234".equals(password)) {
            System.out.println("[SERVICE] 로그인 성공 - username=" + username);
            return true;
        }
        System.out.println("[SERVICE] 로그인 실패 - username=" + username);
        return false;
    }
}
```

#### [2] Aspect 구현 (AOP)

```java
@Aspect
@Component
public class LoginLoggingAspect {
    // AuthService의 login() 메서드에만 적용
    @Pointcut("execution(* com.example.service.AuthService.login(..))")
    public void loginMethod() {}  // 포인트컷 선언, 본문은 비어 있음

    // 로그인 시도 전(before) 실행
    @Before("loginMethod()")
    public void beforeLogin(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.println("[AOP] 로그인 시도 (username: " + args[0] + ")");
    }

    // 로그인 성공 후(after returning) 실행
    @AfterReturning(pointcut = "loginMethod()", returning = "result")
    public void afterLogin(JoinPoint joinPoint, Object result) {
        System.out.println("[AOP] 로그인 결과: " + result);
    }
}
```

- `@Before`: login() 실행 전 실행
- `@AfterReturning`: login() 성공적으로 리턴 후 실행
- `@Pointcut`: Aspect가 적용될 메서드 지정
- `JoinPoint`: 호출 정보 (메서드, 파라미터 등) 확인 가능

#### [3] Controller에서 서비스 호출

```java
@RestController
public class LoginController {
    @Autowired
    private AuthService authService;

    @PostMapping("/login")
    public ResponseEntity<String> login(@RequestParam String username, @RequestParam String password) {
        boolean result = authService.login(username, password);
        return result ? ResponseEntity.ok("로그인 성공") : ResponseEntity.status(401).body("로그인 실패");
    }
}
```

### ✦ 실행 흐름

1. `/login` 요청 → Controller → AuthService.login() 호출
2. login() 메서드 진입 **전**에 AOP가 `[AOP] 로그인 시도 (username: ...)` 로그 출력
3. AuthService의 로그인 로직 실행
4. 로그인 **성공/실패 후**에 `[AOP] 로그인 결과: true/false` 로그 출력

→ Service 코드에 직접 로그 코드 삽입 X, **부가 기능 분리** 가능

---

## 3. Spring @Transactional (트랜잭셔널)

### ✦ 개념

`@Transactional`은 **트랜잭션(일련의 DB 작업의 원자성 보장)** 처리를 AOP로 지원하는 어노테이션입니다.

- 한 메서드 내 여러 DB 작업이 모두 성공해야 **commit**
- 하나라도 실패하면 **rollback**
- 주로 서비스(Service) 계층에서 선언

### ✦ 동작 원리

- Spring AOP가 프록시를 통해 @Transactional이 붙은 메서드를 감싸고 있음
- 메서드 진입 시 **트랜잭션 시작**
- 정상적으로 리턴하면 **commit**, 예외 발생시 **rollback**

#### [예시 코드]

```java
@Service
public class MemberService {
    @Transactional
    public void registerMember(Member member) {
        memberRepository.save(member); // 회원 정보 저장
        emailService.sendWelcomeEmail(member); // 환영 메일 발송 (여기서 예외 발생 가능)
    }
}
```

- 위 예시에서 `sendWelcomeEmail()`에서 예외 발생시, **member 저장도 롤백**됨

#### 트랜잭션 관리 예시 (가장 많이 쓰는 옵션)

- 기본값: **런타임 예외(unchecked exception)** 발생 시 rollback, 체크 예외(checked exception) 발생 시 commit

```java
@Transactional(rollbackFor = Exception.class) // 모든 예외 발생시 롤백
public void someMethod() {...}
```

### ✦ 실무 TIP

- DB에 여러 테이블 insert/update 등 변경 작업이 있을 때 꼭 사용
- 단일 조회(SELECT)에는 불필요

---

## 4. 프록시 기반 AOP와 @Transactional의 관계

- 둘 다 **Spring 프록시(Proxy)** 기반으로 작동
- @Transactional은 내부적으로 AOP로 트랜잭션 처리를 적용
- AOP와 @Transactional을 같이 사용하면,
  1. 트랜잭션 Advice가 먼저 적용되고
  2. 그 외 Aspect도 함께 적용됨

---

## 5. 한눈에 보기: 전체 흐름

1. 클라이언트 요청
2. 프록시 객체가 Controller/Service 메서드 호출 가로챔
3. **@Transactional**이 있으면 트랜잭션을 시작
4. **Aspect** Advice가 있으면 부가 기능 실행
5. 핵심 비즈니스 로직 실행
6. 결과에 따라 트랜잭션 commit/rollback
7. 결과 반환

---

## 6. 표현식 및 포인트컷 설명

### ✦ `execution(* com.example.service.AuthService.login(..))` 설명

- `execution(...)`: 메서드 실행에 Advice를 적용
- `*` : 리턴 타입(아무거나)
- `com.example.service.AuthService` : 클래스 전체 경로
- `.login` : 메서드명
- `(..)` : 파라미터 개수, 타입 상관없이 모두 적용

### ✦ `public void loginMethod() {}` 가 비어있는 이유

- 실제 실행 X, **Pointcut 선언(마커 역할)**
- Advice 어노테이션에서 "이 포인트컷 규칙을 참조"하는 용도
- 예: `@Before("loginMethod()")`, `@AfterReturning("loginMethod()")`

---

## 7. 요약

- **AOP**: 핵심 로직과 부가 기능(공통 관심사) 분리 → 코드 중복, 유지보수 비용 줄임
- **@Transactional**: 트랜잭션 처리를 AOP 방식으로 자동화, 데이터 일관성 보장
- **표현식**: 복잡한 포인트컷을 이름 붙여 재사용할 수 있음
- **실무**: 로깅, 트랜잭션, 보안 등 다양한 영역에서 널리 사용

---

### 추가 질문 있으면 언제든 문의 주세요!
