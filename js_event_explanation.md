
# 📘 자바스크립트 주요 이벤트 설명

JavaScript에서 자주 사용하는 주요 이벤트들을 정리한 문서입니다.  
이벤트는 **사용자의 행동**이나 **브라우저의 상태 변화**를 감지하여 반응하도록 도와주는 중요한 기능입니다.

---

## ✅ 1. `click` 이벤트

### 🔹 개념
사용자가 **마우스를 클릭**했을 때 발생하는 이벤트

### 🔹 사용 예시

```javascript
document.getElementById("myButton").addEventListener("click", function() {
  alert("버튼 클릭됨!");
});
```

---

## ✅ 2. `dblclick` 이벤트

### 🔹 개념
사용자가 **마우스를 두 번 연속 클릭**했을 때 발생하는 이벤트

### 🔹 사용 예시

```javascript
document.getElementById("myButton").addEventListener("dblclick", function() {
  alert("더블 클릭됨!");
});
```

---

## ✅ 3. `mouseover` / `mouseout` 이벤트

### 🔹 개념
- **`mouseover`**: 마우스 커서가 요소 위에 올라갔을 때 발생
- **`mouseout`**: 마우스 커서가 요소를 벗어날 때 발생

### 🔹 사용 예시

```javascript
const element = document.getElementById("myElement");

element.addEventListener("mouseover", function() {
  console.log("마우스가 요소 위로 올라감");
});

element.addEventListener("mouseout", function() {
  console.log("마우스가 요소 밖으로 나감");
});
```

---

## ✅ 4. `keydown`, `keyup`, `keypress` 이벤트

### 🔹 개념
- **`keydown`**: 키를 눌렀을 때 발생
- **`keyup`**: 키에서 손을 뗐을 때 발생
- **`keypress`**: 입력 가능한 키를 눌렀을 때 발생 (주로 문자가 입력될 때)

### 🔹 사용 예시

```javascript
document.addEventListener("keydown", function(event) {
  console.log(`누른 키: ${event.key}`);
});
```

---

## ✅ 5. `change` 이벤트

### 🔹 개념
`input`, `textarea`, `select` 요소에서 값이 **변경**될 때 발생

### 🔹 사용 예시

```javascript
document.getElementById("mySelect").addEventListener("change", function() {
  alert("값이 변경되었습니다!");
});
```

---

## ✅ 6. `input` 이벤트

### 🔹 개념
사용자가 **입력 필드에 텍스트를 입력**할 때마다 발생 (즉시 반영)

### 🔹 사용 예시

```javascript
document.getElementById("myInput").addEventListener("input", function() {
  console.log("입력 값: " + this.value);
});
```

---

## ✅ 7. `focus` / `blur` 이벤트

### 🔹 개념
- **`focus`**: 입력 요소에 포커스가 갔을 때 발생
- **`blur`**: 입력 요소에서 포커스가 벗어날 때 발생

### 🔹 사용 예시

```javascript
const inputField = document.getElementById("myInput");

inputField.addEventListener("focus", function() {
  console.log("입력 필드에 포커스가 갔습니다.");
});

inputField.addEventListener("blur", function() {
  console.log("입력 필드에서 포커스가 벗어났습니다.");
});
```

---

## ✅ 8. `submit` 이벤트

### 🔹 개념
`form` 요소에서 **제출** 버튼을 클릭하거나 엔터를 눌렀을 때 발생

### 🔹 사용 예시

```javascript
document.getElementById("myForm").addEventListener("submit", function(event) {
  event.preventDefault(); // 폼 제출을 막고 사용자 정의 동작 실행
  console.log("폼 제출됨");
});
```

---

## ✅ 9. `resize` 이벤트

### 🔹 개념
**브라우저 창**의 크기가 변경될 때 발생

### 🔹 사용 예시

```javascript
window.addEventListener("resize", function() {
  console.log("창 크기 변경됨");
});
```

---

## ✅ 10. `scroll` 이벤트

### 🔹 개념
사용자가 **스크롤**을 내리거나 올릴 때 발생

### 🔹 사용 예시

```javascript
window.addEventListener("scroll", function() {
  console.log("스크롤 위치: " + window.scrollY);
});
```

---

## ✅ 11. `load` / `DOMContentLoaded` 이벤트

### 🔹 개념
- **`load`**: 모든 리소스(이미지, 스타일시트 등)가 로드된 후 발생
- **`DOMContentLoaded`**: HTML 문서만 로드된 후 발생 (이미지, 스타일시트는 로드되지 않아도 됨)

### 🔹 사용 예시

```javascript
window.addEventListener("load", function() {
  console.log("모든 리소스가 로드되었습니다.");
});

document.addEventListener("DOMContentLoaded", function() {
  console.log("DOM 트리가 완성되었습니다.");
});
```

---

## ✅ 12. `drag` / `drop` 이벤트

### 🔹 개념
- **`drag`**: 사용자가 요소를 **드래그**할 때 발생
- **`drop`**: 드래그한 요소를 **드롭**했을 때 발생

### 🔹 사용 예시

```javascript
const dragItem = document.getElementById("dragItem");

dragItem.addEventListener("dragstart", function(event) {
  console.log("드래그 시작");
});

dragItem.addEventListener("drop", function(event) {
  console.log("아이템을 드롭함");
});
```

---

## 📌 요약

| 이벤트 | 설명 | 사용 예시 |
|--------|------|----------|
| `click` | 마우스 클릭 | 버튼 클릭 시 |
| `dblclick` | 더블 클릭 | 텍스트 블록 강조 |
| `mouseover` / `mouseout` | 마우스 오버/아웃 | 메뉴 활성화 |
| `keydown` / `keyup` / `keypress` | 키보드 입력 | 키보드 이벤트 처리 |
| `change` | 값 변경 | 셀렉트 박스 선택 시 |
| `input` | 실시간 입력 | 텍스트박스에 입력 |
| `focus` / `blur` | 포커스 | 입력 필드 포커스 |
| `submit` | 폼 제출 | 폼 데이터 처리 |
| `resize` | 창 크기 변경 | 창 리사이징 시 |
| `scroll` | 스크롤 이동 | 페이지 스크롤 위치 체크 |
| `load` / `DOMContentLoaded` | 리소스 로딩 | 페이지 로딩 완료 후 |
| `drag` / `drop` | 드래그 앤 드롭 | 요소 이동 처리 |

---

## 🎯 자주 사용하는 이벤트는 다양한 상호작용을 제어하는 데 유용하며, 이벤트 위임(Event Delegation)을 통해 더 효율적으로 관리할 수 있습니다.
