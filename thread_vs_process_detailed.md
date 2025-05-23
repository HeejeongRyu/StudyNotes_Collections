
# 📘 스레드와 프로세스의 자세한 설명

---

## ✅ 1. 프로세스(Process)란?

### 🔹 정의
**프로세스**는 실행 중인 프로그램의 인스턴스입니다.

- 프로그램은 코드 자체, 프로세스는 실행 중인 그 코드
- 하나의 프로세스는 **자신만의 메모리 공간, 자원, 스레드**를 가짐
- 운영체제에서 **CPU 스케줄링의 기본 단위**

### 🔹 예시
- 브라우저를 두 개 실행 → 서로 다른 두 개의 프로세스

---

## ✅ 2. 스레드(Thread)란?

### 🔹 정의
**스레드**는 프로세스 내에서 **실제 작업을 수행하는 실행 단위**입니다.

- 경량의 실행 흐름
- **코드, 데이터, 파일 등 자원을 프로세스와 공유**
- 하나의 프로세스는 **여러 개의 스레드**를 가질 수 있음

### 🔹 예시
- 웹 브라우저: UI 스레드, JS 스레드, 다운로드 스레드 등

---

## ✅ 3. 프로세스 vs 스레드 비교

| 항목 | 프로세스 | 스레드 |
|------|----------|--------|
| 개념 | 실행 중인 프로그램 | 프로세스 내 실행 흐름 |
| 메모리 공간 | 독립적 | 공유 |
| 생성 비용 | 무겁다 | 가볍다 |
| 통신 방식 | IPC 필요 | 메모리 공유 |
| 안정성 | 서로 영향 없음 | 하나가 죽으면 전체 영향 가능 |
| 예시 | 워드, 크롬 | 크롬의 탭, 백그라운드 작업 |

---

## ✅ 4. 메모리 구조 차이

```
[ 프로세스 ]
 ├── 코드(Code)
 ├── 데이터(Data)
 ├── 힙(Heap)
 └── 스택(Stack) ← 스레드마다 별도

[ 스레드 ]
 ├── 공유: 코드, 데이터, 힙
 └── 개별: 스택 (지역 변수 저장)
```

---

## ✅ 5. 실무 활용 예시

| 분야 | 활용 |
|------|------|
| 웹 서버 | 요청마다 스레드 생성 |
| 게임 | 렌더링, AI, 사운드 스레드 분리 |
| 데이터 처리 | 병렬 처리로 속도 향상 |
| 채팅 서버 | 사용자당 하나의 스레드 운영 |

---

## ✅ 6. 멀티프로세스 vs 멀티스레드

| 항목 | 멀티프로세스 | 멀티스레드 |
|------|---------------|-------------|
| 설명 | 여러 프로세스 실행 | 한 프로세스 내 여러 스레드 |
| 예시 | 크롬 탭 | 서버의 요청 처리 |
| 장점 | 안정성, 분리 가능 | 성능 우수, 공유 쉬움 |
| 단점 | 자원 낭비 | 동기화, 충돌 위험 |

---

## ✅ 7. 자바에서 스레드 예시

```java
public class MyThread extends Thread {
    public void run() {
        System.out.println("스레드 실행");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();
    }
}
```

또는 Runnable 사용:

```java
Runnable task = () -> System.out.println("Runnable 실행");
new Thread(task).start();
```

---

## ✅ 8. 동기 vs 비동기 / 병렬 vs 동시

| 구분 | 설명 |
|------|------|
| 동기 | 순차적 실행 |
| 비동기 | 기다리지 않고 다음 실행 |
| 병렬 | 여러 CPU에서 동시에 |
| 동시 | 시간 분할 방식으로 동시에 보임 |

---

## ✅ 9. 멀티스레딩의 위험 요소

| 위험 | 설명 |
|------|------|
| 레이스 컨디션 | 자원 충돌 가능성 |
| 데드락 | 서로 자원을 기다림 |
| 기아 상태 | 실행 기회 박탈 |
| 동기화 필요 | `synchronized` 등으로 보호 |

---

## ✅ 10. 실무 전략

| 전략 | 설명 |
|------|------|
| 스레드풀 사용 | 효율적 자원 관리 |
| 동기화 최소화 | 병목 방지 |
| 스레드 수 제한 | CPU 코어 수에 맞춤 |
| 비동기 API 사용 | Java: `CompletableFuture` 등 |

---

## 🎯 마무리 요약

- **프로세스**: 독립된 자원을 가지는 실행 단위
- **스레드**: 프로세스 내 실행 단위, 자원 공유
- 스레드는 빠르지만 안전성 떨어짐
- 실무에서는 멀티스레드 + 동기화 전략 필수
