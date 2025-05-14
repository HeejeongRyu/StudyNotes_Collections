
# 📘 JavaScript AJAX 통신 방법 자세한 설명

---

## ✅ 1. AJAX 개념

- **Asynchronous JavaScript and XML**의 약자
- 페이지를 새로 고치지 않고 서버와 **비동기적으로 통신**
- **XMLHttpRequest(XHR)** 또는 **Fetch API**를 이용
- 오늘날에는 XML보다 **JSON** 사용이 압도적

---

## ✅ 2. AJAX의 주요 특징

| 특징 | 설명 |
|------|------|
| 비동기 처리 | 서버 응답을 기다리는 동안 UI는 정상 작동 |
| 페이지 리로드 불필요 | 페이지 일부만 동적 변경 가능 |
| 다양한 데이터 포맷 지원 | JSON, XML, HTML, Plain Text |
| 서버와 소통 | HTTP 요청/응답 기반 |

---

## ✅ 3. AJAX 통신 흐름

1. **사용자 행동** (버튼 클릭, 스크롤, 페이지 로드 등)
2. **AJAX 요청 생성**
3. **서버로 HTTP 요청 전송 (GET, POST 등)**
4. **서버에서 요청 처리 후 응답**
5. **응답을 받아 클라이언트 페이지 일부 갱신**

---

## ✅ 4. AJAX 통신 방법

### 🔹 4-1. XMLHttpRequest (전통적인 방법)

```javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "https://api.example.com/data", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log("응답 데이터:", xhr.responseText);
  }
};
xhr.send();
```

### 🔹 4-2. Fetch API (현대적 표준)

```javascript
fetch("https://api.example.com/data")
  .then(response => {
    if (!response.ok) throw new Error("네트워크 오류");
    return response.json();
  })
  .then(data => console.log("응답 데이터:", data))
  .catch(error => console.error("에러:", error));
```

### 🔹 4-3. jQuery AJAX (간편한 방법, 구식)

```javascript
$.ajax({
  url: "https://api.example.com/data",
  method: "GET",
  dataType: "json",
  success: function(data) {
    console.log("성공:", data);
  },
  error: function(xhr, status, error) {
    console.error("에러:", error);
  }
});
```

---

## ✅ 5. AJAX 요청 방식 (GET vs POST)

| 구분 | GET | POST |
|------|-----|------|
| 데이터 전달 | URL 쿼리 스트링 | HTTP Body |
| 용도 | 데이터 조회 | 데이터 등록, 수정, 삭제 |
| 데이터 크기 제한 | 있음 (URL 길이 제한) | 상대적으로 큼 |
| 보안 | 노출 위험 | 상대적으로 안전 |

---

## ✅ 6. AJAX 에러 처리

| 오류 | 설명 |
|------|------|
| 네트워크 오류 | 서버 접근 불가 |
| CORS 오류 | 서버가 Cross-Origin 허용하지 않음 |
| 4xx 오류 | 클라이언트 요청 오류 (404, 400 등) |
| 5xx 오류 | 서버 오류 (500, 502 등) |
| JSON 파싱 오류 | 응답 데이터가 잘못됨 |

---

## ✅ 7. 실무에서 AJAX 사용 예시

### 🔹 7-1. 페이지 무한 스크롤

```javascript
window.addEventListener("scroll", function() {
  if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
    fetch("/api/next-page")
      .then(res => res.text())
      .then(html => document.body.insertAdjacentHTML("beforeend", html));
  }
});
```

### 🔹 7-2. 실시간 자동 저장 (Auto Save)

```javascript
setInterval(() => {
  fetch("/api/save", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ content: document.querySelector("#editor").value })
  });
}, 5000);
```

### 🔹 7-3. 비동기 로그인 처리

```javascript
fetch("/api/login", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ username: "user", password: "pass" })
})
  .then(res => res.json())
  .then(data => console.log(data));
```

---

## ✅ 8. AJAX 실무 팁

| 팁 | 설명 |
|-----|------|
| `async/await` 활용 | fetch 사용 시 코드 간결화 |
| `AbortController` | 요청 취소 기능 활용 |
| `loading` UI 표시 | 사용자 경험 개선 |
| `retry` 구현 | 실패 시 재시도 로직 |
| CORS 정책 이해 | 서버와 클라이언트 도메인 이슈 사전 대응 |

---

## 🎯 요약

- AJAX = 클라이언트와 서버 비동기 통신 기술
- Fetch API가 현재 표준
- JSON 데이터 사용이 일반적
- 에러 처리와 사용자 UX 고려 중요
- 무한 스크롤, 자동 저장, 실시간 데이터에 필수
