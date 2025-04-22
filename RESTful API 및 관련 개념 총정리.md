## ✅ RESTful API란?

**REST**(Representational State Transfer)는 HTTP 기반의 **웹 통신 아키텍처 스타일**입니다.  
**RESTful API**는 이 REST 원칙을 따르는 **API(응용 프로그램 인터페이스)**를 의미합니다.

---

## ✅ REST의 6가지 기본 원칙

1. **Client-Server 구조**: 클라이언트와 서버를 명확히 분리
2. **Stateless (무상태성)**: 각각의 요청은 독립적으로 처리
3. **Cacheable (캐시 가능)**: 응답을 캐시할 수 있음
4. **Uniform Interface (일관된 인터페이스)**: 일관된 URI 및 메서드 사용
5. **Layered System (계층화된 시스템)**: 중간 서버를 통한 구조 확장 가능
6. **Code on Demand (선택적)**: 클라이언트에서 서버로부터 코드 다운로드 가능

---

## ✅ RESTful API 구성 요소

- **리소스(Resource)**: 서버의 데이터 객체 (`/users`, `/products`)
- **HTTP 메서드**:
  - `GET`: 데이터 조회
  - `POST`: 데이터 생성
  - `PUT`: 데이터 전체 수정
  - `PATCH`: 데이터 일부 수정
  - `DELETE`: 데이터 삭제
- **URI(Endpoint)**: 리소스를 식별하는 고유 주소 (`GET /users/1`)
- **Representation**: 리소스의 표현 (JSON 또는 XML)

---

## ✅ RESTful API 장점

| 장점                     | 설명 |
|--------------------------|------|
| 단순함과 명확성          | HTTP 메서드로 의미 명확히 표현 가능 |
| 유지보수 용이            | 구조가 일관되고 명료하여 개발 및 유지보수 편리 |
| 다양한 클라이언트 호환   | 웹, 앱, IoT 등과 연동이 쉬움 |
| 독립적 개발 가능         | 서버와 클라이언트 완전 분리 가능 |
| 캐싱 지원                | GET 요청 캐싱 가능으로 성능 향상 |

---

## ✅ Java(Spring) 기반 RESTful API 예제

```java
@RestController
@RequestMapping("/users")
public class UserController {

    // GET: 사용자 조회
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }

    // POST: 사용자 생성
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.saveUser(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }
}
✅ GET vs POST 차이점
항목	GET	POST
목적	리소스 조회	리소스 생성 또는 데이터 전송
데이터 전달	URL 쿼리 스트링 (/users?id=1)	HTTP Body (JSON 등)
URL 길이 제한	있음	없음
보안성	낮음 (URL에 노출됨)	높음 (Body에 숨김)
캐싱 가능 여부	O	X
멱등성	O (여러 번 요청해도 결과 동일)	X (중복 생성 가능성 있음)
사용 예	조회, 검색, 확인	회원가입, 로그인, 데이터 등록 등
✅ 응답 예시 (JSON)
json
복사
편집
{
  "id": 1,
  "name": "Jane",
  "age": 28
}
✅ HTTPS vs RESTful API 차이점
구분	HTTPS	RESTful API
정의	HTTP 프로토콜에 SSL/TLS 보안을 적용한 전송 방식	HTTP 프로토콜을 활용해 리소스를 처리하는 API 방식
목적	통신 암호화, 보안, 무결성 보장	자원 중심 API 설계 및 통신
관계	REST API의 통신을 보호하는 보안 채널	HTTPS 위에서 동작할 수 있음
작동 계층	전송 계층 (Transport Layer)	애플리케이션 계층 (Application Layer)
📌 비유로 설명
RESTful API: 문서를 어떤 구조로 주고받을지 정한 방식

HTTPS: 그 문서를 자물쇠로 잠가서 안전하게 전달하는 통로

✅ 마무리 요약
RESTful API는 HTTP 기반의 리소스 중심 설계

GET/POST는 목적과 사용 방식에 따라 명확히 구분 필요

실제 서비스에서는 HTTPS와 REST API를 함께 사용하여 보안 + 효율 모두 확보하는 것이 중요
