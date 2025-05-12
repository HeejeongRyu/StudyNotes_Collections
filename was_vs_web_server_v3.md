
# 📘 WAS (Web Application Server) vs Web Server

---

## ✅ Web Server (웹 서버)

### 🔹 정의
정적 콘텐츠(HTML, CSS, JS, 이미지 등)를 클라이언트에게 제공하는 서버.  
클라이언트의 요청을 받아 **정적 리소스**를 반환하거나, WAS로 동적 요청을 전달.

### 🔹 역할
| 역할 | 설명 |
|------|------|
| 정적 리소스 제공 | HTML, CSS, JS, 이미지 등 전달 |
| 요청 처리 | HTTP/HTTPS 요청 수신 및 응답 |
| 리버스 프록시 | WAS, API 서버로 요청 전달 |
| 부하 분산 | 여러 WAS에 트래픽 분산 가능 |

### 🔹 대표 제품
- Nginx
- Apache HTTP Server
- Microsoft IIS

---

## ✅ WAS (Web Application Server, 웹 애플리케이션 서버)

### 🔹 정의
동적 웹 애플리케이션의 로직을 처리하는 서버.  
사용자 요청을 받아 비즈니스 로직을 처리하고, DB에 접근해 동적 페이지나 API 응답을 생성.

### 🔹 역할
| 역할 | 설명 |
|------|------|
| 동적 컨텐츠 생성 | JSP, Servlet, Spring 등 실행 |
| 비즈니스 로직 처리 | 주문, 로그인, 세션 관리 등 |
| DB 연동 | 데이터 조회, 처리 |
| API 응답 | REST API, JSON 생성 및 응답 |

### 🔹 대표 제품
- Apache Tomcat
- WebLogic (Oracle)
- JEUS (TmaxSoft)
- JBoss (RedHat)

---

## ✅ Web Server vs WAS 비교

| 구분 | Web Server | WAS |
|------|------------|-----|
| 주 기능 | 정적 리소스 제공 | 동적 비즈니스 로직 처리 |
| 처리 대상 | HTML, CSS, JS, 이미지 | JSP, Servlet, API |
| 세션 관리 | X | O |
| DB 접근 | X | O |
| 속도 | 빠름 | 상대적으로 무거움 |

---

## ✅ 아키텍처 흐름

```
[Client (Browser)]
        │
    HTTP/HTTPS 요청
        │
[Web Server (Nginx, Apache HTTP)]
    ├─ 정적 리소스 직접 응답
    └─ 동적 요청 WAS 전달
        │
[WAS (Tomcat, JEUS)]
    ├─ 비즈니스 로직 실행
    ├─ DB 연동
    └─ 동적 페이지/JSON 응답
```

---

## ✅ 실무에서 둘을 나누는 이유

| 이유 | 설명 |
|------|------|
| 성능 최적화 | 정적 요청은 빠르게 Web Server에서 처리 |
| 보안 강화 | WAS는 외부에 직접 노출되지 않음 |
| 관리 편의성 | 역할 구분으로 장애 원인 식별 용이 |
| 확장성 | WAS만 독립적으로 수평 확장 가능 |

---

## ✅ 부가적으로 알아야 할 개념

| 용어 | 설명 |
|------|------|
| Reverse Proxy | Web Server가 WAS 앞단에서 요청 받아 전달 |
| Load Balancer | WAS 여러 대에 부하 분산 |
| API Gateway | API 요청 통합 처리, 인증/보안 포함 |
| CDN | 정적 리소스 전 세계 분산 제공 |

---

## 🎯 요약

- Web Server: **정적 리소스 제공**
- WAS: **동적 로직 및 데이터 처리**
- 실무 표준: **Web Server + WAS 조합 사용**
