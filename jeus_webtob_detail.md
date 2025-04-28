
# 📘 JEUS (제우스) / WebtoB (웹투비) 자세한 설명

---

## 🧩 1. JEUS란?

**JEUS (Java Enterprise User Solution)** 는  
**티맥스소프트(TmaxSoft)** 에서 개발한 **국산 상용 WAS(Web Application Server)**입니다.

| 항목 | 설명 |
|:---|:---|
| 정식 명칭 | Java EE 기반 웹 애플리케이션 서버 |
| 주요 기능 | Servlet/JSP 실행, EJB 지원, 트랜잭션 관리, 세션 관리, 로드밸런싱 |
| 역할 | 비즈니스 로직 수행, DB 연결, 애플리케이션 서버 역할 |
| 특징 | 고가용성(HA) 지원, 트랜잭션 처리 최적화, WebtoB 연동 최적화 |
| 사용 분야 | 금융, 통신, 공공기관 등 대규모 엔터프라이즈 시스템 |

**JEUS의 핵심 역할**
- Java 기반 웹 애플리케이션을 실행
- 트랜잭션, 세션, 데이터베이스 접속 관리
- 서버 간 클러스터링, 부하 분산, 장애 복구 지원

---

## 🧩 2. WebtoB란?

**WebtoB**는  
**티맥스소프트**가 개발한 고성능 **HTTP 웹 서버**입니다.

| 항목 | 설명 |
|:---|:---|
| 정식 명칭 | WebtoB (Web to Backend) |
| 주요 기능 | HTTP/HTTPS 요청 수신, 요청 라우팅, SSL 처리 |
| 역할 | 외부 요청을 받아 JEUS로 전달, 결과를 사용자에게 응답 |
| 특징 | JEUS와 소켓 직접 연결 최적화, HTTP/2 지원, SSL 오프로드 |
| 사용 분야 | 웹 서버 앞단, 로드밸런서, 리버스 프록시 대용 |

**WebtoB의 핵심 역할**
- HTTP 요청 수신
- 요청을 분석하여 JEUS 서버에 분배
- SSL 처리를 대행하고, WAS 부하를 줄임
- JEUS 죽음 감지 및 Failover 지원

---

## 🔥 3. WebtoB ↔ JEUS 연동 관계

| 구분 | 역할 |
|:---|:---|
| WebtoB | 사용자 요청을 수신하여 JEUS로 전달 |
| JEUS | 요청에 대한 실제 비즈니스 로직 수행 |
| 통신 방식 | 소켓(Socket) 연결 (9000~10000 포트) |
| 설정 파일 | `wsl.conf`, `webtob.properties` |
| 연결 관리 | WebtoB가 여러 JEUS 서버와 동시 연결, 로드밸런싱/Failover 지원 |

### 연결 순서
1. 사용자 → WebtoB (80/443 포트 Listen)
2. WebtoB → JEUS (9000포트 등으로 소켓 연결)
3. JEUS → DB 등 내부 자원 접근 후 응답 생성
4. JEUS → WebtoB 응답 전달
5. WebtoB → 사용자 브라우저로 응답 전송

---

## 📋 WebtoB 주요 설정 파일

| 파일명 | 설명 |
|:---|:---|
| wstenv.conf | WebtoB 전체 환경설정 |
| wsl.conf | JEUS 연결 서버 리스트 설정 |
| wsconfig.conf | WebtoB 서버 자체 설정 |
| wsroute.conf | 요청 URL 라우팅 설정 |

---

## 📋 JEUS 주요 설정 파일

| 파일명 | 설명 |
|:---|:---|
| web.xml | Java 웹 애플리케이션 기본 설정 |
| jeus.xml | JEUS 서버 기동 설정 |
| webtob.properties | WebtoB 연결용 포트/주소 설정 |
| domain.xml | 도메인 클러스터링 설정 |

---

## ⚙️ WebtoB ↔ JEUS 설정 포인트 요약

| 항목 | WebtoB 설정 | JEUS 설정 |
|:---|:---|:---|
| 서버 IP/Port | wsl.conf에 JEUS IP/포트 등록 | webtob.properties에 포트 설정 |
| 연결 프로토콜 | Type jeus 지정 | protocol=jeus 지정 |
| 장애 대비 | 여러 서버 등록 (Failover 가능) | Thread 수, Connection 수 튜닝 |


---

## 📜 최종 요약

- JEUS는 Java 웹 애플리케이션 실행을 담당하는 WAS입니다.
- WebtoB는 HTTP 요청을 받아 JEUS로 전달하는 고성능 웹서버입니다.
- 둘은 소켓 통신을 통해 연동되며, 각 설정 파일을 통해 관리합니다.
- 고가용성, 부하분산, 장애복구 설정이 가능합니다.

> 🎯 WebtoB는 '외부 문을 여는 역할', JEUS는 '내부 비즈니스 로직을 처리하는 역할'입니다.
