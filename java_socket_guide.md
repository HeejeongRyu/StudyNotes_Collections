
# 📘 Java 소켓 통신(Socket Communication) 완전 정리

---

## ✅ 1. 소켓(Socket)이란?

소켓은 네트워크를 통해 데이터를 송수신하기 위한 **종단점(End Point)**입니다.  
Java에서는 `java.net` 패키지를 통해 소켓 통신을 구현할 수 있습니다.

---

## ✅ 2. Java 소켓 통신 구조

소켓 통신은 **클라이언트(Client)**와 **서버(Server)** 구조로 이루어집니다.

### 🔹 Server 역할
- 특정 포트를 열고 클라이언트의 연결을 기다림
- 클라이언트가 연결하면 소켓을 생성하여 통신

### 🔹 Client 역할
- 서버의 IP와 포트로 연결 요청을 보냄
- 연결되면 소켓을 통해 데이터 송수신

---

## ✅ 3. 서버 측 코드 예시

```java
import java.io.*;
import java.net.*;

public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(12345); // 포트 12345
        System.out.println("서버 대기 중...");

        Socket clientSocket = serverSocket.accept(); // 클라이언트 연결 수락
        System.out.println("클라이언트 연결됨!");

        BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
        PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);

        String message;
        while ((message = in.readLine()) != null) {
            System.out.println("클라이언트: " + message);
            out.println("서버: " + message.toUpperCase());
        }

        in.close();
        out.close();
        clientSocket.close();
        serverSocket.close();
    }
}
```

---

## ✅ 4. 클라이언트 측 코드 예시

```java
import java.io.*;
import java.net.*;

public class Client {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 12345); // 서버에 연결

        BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        BufferedReader userInput = new BufferedReader(new InputStreamReader(System.in));

        String input;
        while ((input = userInput.readLine()) != null) {
            out.println(input);
            System.out.println("서버 응답: " + in.readLine());
        }

        in.close();
        out.close();
        socket.close();
    }
}
```

---

## ✅ 5. 관련 개념 설명

### 🔸 TCP vs UDP
| 구분 | TCP (Socket) | UDP (Datagram) |
|------|---------------|----------------|
| 연결 방식 | 연결 지향 | 비연결 지향 |
| 신뢰성 | 높음 (순서 보장, 재전송) | 낮음 |
| 속도 | 상대적으로 느림 | 빠름 |
| 예시 | 채팅, 파일 전송 | 스트리밍, 게임 |

---

### 🔸 ServerSocket & Socket 클래스

| 클래스 | 설명 |
|--------|------|
| `ServerSocket` | 서버 측에서 연결을 대기하는 클래스 |
| `Socket` | 실제 데이터 송수신에 사용되는 클래스 |

---

### 🔸 Stream (Input/Output)

- `InputStream` / `OutputStream`: 바이트 단위 전송
- `Reader` / `Writer`: 문자 단위 전송 (BufferedReader, PrintWriter 등과 자주 사용)

---

### 🔸 멀티스레드 서버

> 하나의 서버가 여러 클라이언트 요청을 동시에 처리하려면 **스레드(Thread)**로 클라이언트별 처리가 필요합니다.

---

## ✅ 6. 자주 발생하는 이슈

| 이슈 | 설명 |
|------|------|
| 포트 충돌 | 이미 사용 중인 포트를 열려고 할 때 오류 발생 |
| 데이터가 안 오는 현상 | 버퍼가 비워지지 않거나 flush 안 함 |
| 연결 지연/끊김 | 클라이언트나 서버 쪽 close 누락 또는 네트워크 문제 |

---

## 📌 요약

- Java 소켓 통신은 `ServerSocket`과 `Socket` 클래스를 기반으로 함
- 서버는 포트를 열고 클라이언트를 대기
- 클라이언트는 IP와 포트로 연결 요청
- 데이터 송수신은 스트림 기반으로 처리
- 멀티 클라이언트 처리는 스레드 사용

> 🎯 실시간 채팅, 메시지 브로커, IoT 디바이스 통신 등에서 활용됩니다.
