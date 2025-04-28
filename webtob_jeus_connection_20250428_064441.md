
# 📘 WebtoB ↔ JEUS 연결 및 로드밸런싱 구조 정리

---

## 🧩 1. WebtoB → JEUS 기본 구조

```
[사용자 브라우저]
    ↓ (HTTP 요청)

┌─────────────────────────────────────────────────┐
│        WebtoB (Listen 80/443 Port)               │
└─────────────────────────────────────────────────┘
    ↓ (Socket 연결, ex. 9000 포트)

┌─────────────────────────────────────────────────┐
│               JEUS (Listen 9000 Port)            │
└─────────────────────────────────────────────────┘
```

---

## 🔧 2. WebtoB 설정 파일 (`wsl.conf`) 예시

```bash
WSL JEUS_CLUSTER
{
    Name JEUS1
    Type jeus
    Address 192.168.100.101
    Port 9000
    Weight 1
}

WSL JEUS_CLUSTER
{
    Name JEUS2
    Type jeus
    Address 192.168.100.102
    Port 9000
    Weight 1
}

WSL JEUS_CLUSTER
{
    Name JEUS3
    Type jeus
    Address 192.168.100.103
    Port 9000
    Weight 2
}
```

✅ 같은 그룹 이름(JEUS_CLUSTER)으로 묶여 있어야 로드밸런싱이 적용됩니다.  
✅ `Weight` 값으로 서버별 부하 분산 비율 조정 가능합니다.

---

## 🔧 3. JEUS 설정 파일 (`webtob.properties`) 예시

```properties
webtob.connector.port=9000
webtob.connector.address=192.168.100.101
webtob.connector.protocol=jeus
```

✅ JEUS는 `9000` 포트에서 WebtoB 요청을 대기해야 합니다.

---

## 🔥 4. WebtoB → JEUS 로드밸런싱 동작 과정

1. 사용자가 HTTP 요청을 WebtoB에 전송 (ex. 80/443 포트)
2. WebtoB는 `wsl.conf`에 설정된 JEUS 서버들 중 하나를 선택
3. 선택된 JEUS 서버로 Socket 연결 후 요청 전달
4. JEUS가 요청 처리 후 결과를 WebtoB에 반환
5. WebtoB가 최종 응답을 사용자에게 반환

---

## ⚠️ 5. 장애 발생 시 확인 포인트

| 장애 구간 | 원인 |
|:---|:---|
| WebtoB가 JEUS 연결 실패 | IP/Port 오타, 방화벽 차단, JEUS 미기동 |
| Handshaking 실패 (EOFException) | 포트 연결은 성공했으나 프로토콜 불일치 |
| 요청은 전달됐지만 응답 없음 | JEUS 서버 과부하, Thread 고갈, Application 에러 |

---

## 📜 6. 전체 요약

| 핵심 포인트 | 설명 |
|:---|:---|
| WebtoB 역할 | 사용자 HTTP 요청 수신 후 JEUS에 전달 |
| JEUS 역할 | 비즈니스 로직 실행 후 응답 반환 |
| 설정 주의사항 | wsl.conf, webtob.properties IP, Port 일치 필수 |
| 포트 상태 확인 | `netstat`, `telnet`, `wscmd`, `wscfl` 명령 사용 |

---

## 🛠️ 7. 추가 팁 (Optional)

추가로 설정하거나 운영할 수 있는 것:

- WebtoB Failover 구조 예시
- WebtoB/JEUS 장애 발생 시 재기동 순서
- WebtoB 상태 모니터링 명령어 사용법 (`wscmd`, `wscfl`)

> 🎯 WebtoB와 JEUS는 연결 포트, 그룹 구성, 방화벽 상태가 맞아야 안정적으로 운용할 수 있습니다.
