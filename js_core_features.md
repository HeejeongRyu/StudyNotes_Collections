
# 📘 자바스크립트 주요 기능 정리

이 문서는 실무에서 자주 쓰이는 핵심 JavaScript 기능들을 간단한 예제와 함께 정리한 자료입니다.

---

## ✅ 1. `addEventListener`

```javascript
button.addEventListener("click", function () {
  alert("버튼 클릭됨!");
});
```

- HTML 요소에 다양한 이벤트를 독립적으로 바인딩 가능
- `onclick =`보다 유연하고 실무에서 많이 사용됨

---

## ✅ 2. `querySelector` / `getElementById`

```javascript
const el = document.querySelector(".menu"); // CSS 선택자 기반
const el2 = document.getElementById("header"); // ID 기반
```

- DOM 요소를 선택하는 방법
- `querySelector`는 더 범용적이고 강력함

---

## ✅ 3. 템플릿 리터럴

```javascript
const name = "Tom";
console.log(`Hello, ${name}!`);
```

- 문자열 안에 변수를 `${}`로 삽입
- 백틱(``)을 사용하여 가독성 좋게 작성 가능

---

## ✅ 4. 화살표 함수 (Arrow Function)

```javascript
const add = (a, b) => a + b;
```

- 함수 표현을 간결하게 작성
- `this`를 바인딩하지 않음 → 콜백에서 자주 사용

---

## ✅ 5. Promise / async-await

```javascript
async function fetchData() {
  const response = await fetch("/api/data");
  const data = await response.json();
  console.log(data);
}
```

- 비동기 흐름을 `then()` 대신 `async/await`으로 간결하게 작성
- 네트워크 요청, 시간 지연 처리에 필수

---

## ✅ 6. `setTimeout` / `setInterval`

```javascript
setTimeout(() => console.log("3초 뒤 실행"), 3000);
const id = setInterval(() => console.log("1초마다 실행"), 1000);
```

- 특정 시간 후 실행(`setTimeout`)
- 반복 실행(`setInterval`)

---

## ✅ 7. `localStorage` / `sessionStorage`

```javascript
localStorage.setItem("token", "abc123");
const token = localStorage.getItem("token");
```

- 브라우저에 데이터를 저장
- `localStorage`: 영구 저장, `sessionStorage`: 탭 종료 시 사라짐

---

## ✅ 8. 클래스(Class)

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
}
```

- ES6 이후 자바스크립트도 객체 지향(OOP) 구현 가능
- 생성자, 상속, 메서드 포함

---

## ✅ 9. 이벤트 위임(Event Delegation)

```javascript
document.querySelector("ul").addEventListener("click", function (e) {
  if (e.target.tagName === "LI") {
    console.log("클릭된 항목:", e.target.textContent);
  }
});
```

- 자식 요소마다 이벤트를 따로 등록하지 않고
- **부모 요소에 한 번만 등록**하여 효율적으로 처리

---

## ✅ 10. fetch API

```javascript
fetch("https://api.example.com/data")
  .then(res => res.json())
  .then(data => console.log(data));
```

- HTTP 요청을 위한 브라우저 내장 API
- `XMLHttpRequest`보다 간결하고 최신 문법

---

## 📌 요약

| 기능 | 핵심 설명 |
|------|-----------|
| `addEventListener` | 다양한 이벤트를 유연하게 등록 |
| `querySelector` | CSS 문법 기반 DOM 선택 |
| 템플릿 리터럴 | 가독성 좋은 문자열 조합 |
| 화살표 함수 | 간결한 함수, `this` 바인딩 없음 |
| async/await | 비동기 흐름을 동기처럼 표현 |
| `setTimeout` / `setInterval` | 지연 또는 반복 실행 |
| `localStorage` | 클라이언트에 상태 저장 |
| 클래스 | 객체 지향 코드 작성 가능 |
| 이벤트 위임 | 효율적인 이벤트 처리 |
| fetch | 네트워크 요청을 간편하게 |

> 🎯 이 문서는 JS 실무 개발, 프론트 학습의 핵심 요약 자료로 활용할 수 있습니다.
