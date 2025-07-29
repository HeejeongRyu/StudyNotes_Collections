
# Spring AOP (Aspect Oriented Programming) 상세 설명 + 로그인 예시

## 1️⃣ AOP란 무엇인가?

Spring AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)는 **핵심 로직과 공통 관심사(Cross-Cutting Concerns)를 분리하여 관리하는 프로그래밍 기법**입니다.  
일반적인 OOP(객체 지향 프로그래밍)는 클래스 단위로 관심사를 모듈화하지만, **AOP는 여러 클래스에 공통적으로 적용되는 기능(횡단 관심사)을 분리**합니다.

### ✅ AOP의 필요성
- 로깅, 트랜잭션 관리, 보안 체크, 성능 모니터링 같은 기능은 여러 모듈에서 반복적으로 사용됨.
- 이런 공통 코드를 매번 서비스 로직에 넣으면 **중복 코드** 발생 → 유지보수 어려움.
- **AOP는 이런 코드들을 Aspect로 분리하여 핵심 로직과 부가 기능을 깔끔하게 분리**합니다.

---

## 2️⃣ Spring AOP의 핵심 개념

- **Aspect** : 공통 관심사를 모아둔 모듈 (예: LoggingAspect, TransactionAspect)
- **JoinPoint** : AOP가 적용될 수 있는 모든 지점 (메서드 실행, 예외 발생 등)
- **Pointcut** : 어떤 메서드에 Aspect를 적용할지 지정하는 표현식  
  예) `execution(* com.example.service.*.*(..))`
- **Advice** : 실제 수행될 공통 코드
  - `@Before` : 메서드 실행 전
  - `@After` : 메서드 실행 후 (무조건)
  - `@AfterReturning` : 정상 리턴 후
  - `@AfterThrowing` : 예외 발생 시
  - `@Around` : 메서드 실행 전후 모두 제어 가능
- **Weaving** : Pointcut + Advice를 실제 코드에 적용하는 과정

---

## 3️⃣ Spring AOP 사용하는 이유

1. **중복 코드 제거 → 유지보수성 향상**
2. **핵심 로직과 부가 기능 분리 → 가독성 증가**
3. **운영 환경에서 기능 추가/변경 용이**
4. **테스트 용이**

---

## 4️⃣ Pointcut 표현식 예시

```java
@Pointcut("execution(* com.example.service.*.*(..))")
public void serviceMethods() {}
```

- `execution(* com.example.service.*.*(..))`
  - `com.example.service` 패키지
  - 모든 클래스(`*`)
  - 모든 메서드(`*`)
  - 파라미터 상관없음(`(..)`)
- **하위 패키지까지 포함하려면 `..` 사용**
  ```java
  @Pointcut("execution(* com.example.service..*.*(..))")
  ```

---

## 5️⃣ XML 기반 vs 어노테이션 기반 설정

### ✅ XML 기반 설정
- 설정을 `applicationContext.xml`에서 정의
- 코드와 설정 분리

```xml
<bean id="loggingAspect" class="com.example.LoggingAspect"/>

<aop:config>
    <aop:aspect ref="loggingAspect">
        <aop:pointcut id="serviceMethods" expression="execution(* com.example.service.*.*(..))"/>
        <aop:before pointcut-ref="serviceMethods" method="logBefore"/>
    </aop:aspect>
</aop:config>
```

### ✅ 어노테이션 기반 설정
- `@Aspect` + `@EnableAspectJAutoProxy` 사용
- 코드 내부에서 Pointcut과 Advice 정의

```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    @Before("serviceMethods()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("[LOG] 실행 전: " + joinPoint.getSignature());
    }
}
```

➡️ **Spring Boot 환경에서는 어노테이션 기반이 표준**

---

## 6️⃣ 로그인 예시로 보는 AOP 적용

### 📌 Controller

```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    @Autowired
    private AuthService authService;

    @PostMapping("/login")
    public String login(@RequestParam String username, @RequestParam String password) {
        boolean result = authService.login(username, password);
        return result ? "로그인 성공" : "로그인 실패";
    }
}
```

### 📌 Service (핵심 로그인 로직)

```java
@Service
public class AuthService {

    public boolean login(String username, String password) {
        System.out.println("[SERVICE] 로그인 시도 - username=" + username);

        // DB 조회나 인증 서버 검증 대신 간단한 하드코딩 예시
        if ("admin".equals(username) && "1234".equals(password)) {
            System.out.println("[SERVICE] 로그인 성공 - username=" + username);
            return true;
        }

        System.out.println("[SERVICE] 로그인 실패 - username=" + username);
        return false;
    }
}
```

### 📌 AOP Aspect (로그 기록)

```java
@Aspect
@Component
public class LoginLoggingAspect {

    // AuthService.login(..) 메서드만 Pointcut으로 지정
    @Pointcut("execution(* com.example.service.AuthService.login(..))")
    public void loginMethod() {}

    @Before("loginMethod()")
    public void logBefore(JoinPoint joinPoint) {
        String username = (String) joinPoint.getArgs()[0];
        System.out.println("[AOP] 로그인 시도: 사용자=" + username + ", 시간=" + LocalDateTime.now());
    }

    @AfterReturning(pointcut = "loginMethod()", returning = "result")
    public void logAfter(JoinPoint joinPoint, Object result) {
        String username = (String) joinPoint.getArgs()[0];
        boolean success = (boolean) result;
        System.out.println("[AOP] 로그인 결과: 사용자=" + username + ", 성공여부=" + success);
    }

    @AfterThrowing(pointcut = "loginMethod()", throwing = "ex")
    public void logException(JoinPoint joinPoint, Exception ex) {
        System.out.println("[AOP] 로그인 중 예외 발생: " + ex.getMessage());
    }
}
```

---

## 7️⃣ 실행 순서 (정상 로그인)

### 1. 사용자가 로그인 요청
```
POST /auth/login
username=admin&password=1234
```

### 2. Controller 호출
```java
boolean result = authService.login(username, password);
```

### 3. Spring AOP 프록시 개입
- `AuthService`는 Spring Bean → Spring이 프록시 객체 생성
- `authService.login()` 호출 시 프록시가 먼저 실행
- `execution(* com.example.service.AuthService.login(..))` Pointcut과 매칭

### 4. @Before 실행
```
[AOP] 로그인 시도: 사용자=admin, 시간=2025-07-28T11:10
```

### 5. 실제 Service 메서드 실행
```
[SERVICE] 로그인 시도 - username=admin
[SERVICE] 로그인 성공 - username=admin
```

### 6. @AfterReturning 실행
```
[AOP] 로그인 결과: 사용자=admin, 성공여부=true
```

### 7. Controller 반환
- `"로그인 성공"` → 브라우저 응답

---

## 8️⃣ 예외 발생 시 순서

1. Controller → `authService.login()` 호출
2. `@Before` 실행 (로그 시도 기록)
3. Service 메서드 실행 중 예외 발생
4. `@AfterThrowing` 실행 → 예외 로그 출력
5. 컨트롤러로 예외 전달 → 500 에러 or Exception Handler 처리

---

## 9️⃣ 중요한 점: 왜 Service 코드에 AOP 코드가 없는데 실행되는가?

- Spring AOP는 **프록시 기반**으로 동작.
- `@Service` Bean을 그대로 쓰지 않고 **프록시 객체**를 만들어 컨테이너에 등록.
- 컨트롤러에서 `authService.login()` 호출 시 → 실제 AuthService가 아니라 프록시가 먼저 실행.
- 프록시가 Pointcut 매칭 확인 후, `@Before` → 실제 서비스 → `@AfterReturning` 순으로 실행.

### 📌 구조
```
Controller
   ↓
[프록시 객체]   <-- Spring이 만든 가짜 AuthService
   ├─ @Before 실행
   ├─ 실제 AuthService.login() 호출
   ├─ @AfterReturning 실행
   ↓
Service 결과 반환
```

---

## 🔟 요약
- **AOP = 핵심 로직과 공통 관심사 분리**
- **Aspect = 공통 관심사 모듈**
- **Pointcut = AOP 적용 범위**
- **Advice = 실제 수행될 코드**
- **Spring AOP = 프록시 객체로 런타임에 가로채기**
- 로그인 예시에서 AOP는 로그인 시도/결과를 로깅, 예외 발생 시 기록을 담당

---
