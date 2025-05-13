
# 📘 JavaScript 화살표 함수(Arrow Function) 자세한 설명

---

## ✅ 1. 기본 개념

```javascript
const add = (a, b) => {
  return a + b;
};
```

- `function` 키워드 대신 `=>` 기호 사용
- 함수 표현식의 간결한 대안
- 익명 함수로 주로 사용

---

## ✅ 2. 문법

### 🔹 일반 함수 vs 화살표 함수

```javascript
// 일반 함수
function add(a, b) {
  return a + b;
}

// 화살표 함수
const add = (a, b) => a + b;
```

### 🔹 매개변수가 하나일 때 괄호 생략 가능

```javascript
const square = x => x * x;
```

### 🔹 매개변수가 없으면 괄호 필수

```javascript
const greet = () => console.log("Hello");
```

### 🔹 중괄호 사용 시 return 필수

```javascript
const multiply = (a, b) => {
  const result = a * b;
  return result;
};
```

### 🔹 객체 리터럴 반환 시 괄호로 감싸야 함

```javascript
const getUser = () => ({ name: "Tom", age: 20 });
```

---

## ✅ 3. 특징

| 항목 | 화살표 함수 | 일반 함수 |
|------|--------------|------------|
| `this` 바인딩 | 상위 컨텍스트의 this 사용 (lexical this) | 호출 시점의 this |
| `arguments` 객체 | 미지원 | 지원 |
| 생성자 사용 | ❌ | O |
| 메서드 정의 | 권장 X | O |

---

## ✅ 4. 화살표 함수의 `this` 동작

```javascript
function Person() {
  this.age = 0;

  setInterval(() => {
    this.age++; // 화살표 함수 → Person 인스턴스의 this 유지
    console.log(this.age);
  }, 1000);
}
new Person();
```

---

## ✅ 5. 사용 시 주의사항

| 주의 | 이유 |
|------|------|
| 메서드 정의에 사용 금지 | 클래스/객체 메서드는 일반 함수 추천 (this 문제) |
| 이벤트 핸들러에서는 신중히 사용 | this가 이벤트 대상이 아님 |
| arguments 객체 필요시 사용 불가 | Rest Parameter (...args) 사용 필요 |
| 생성자 불가 | new 키워드 사용 불가 |

---

## ✅ 6. 실전 예제 모음

### 🔹 배열 메서드에서 콜백 함수

```javascript
const nums = [1, 2, 3];
const doubled = nums.map(n => n * 2);
```

### 🔹 이벤트 핸들러에서 this 관리

```javascript
function Counter() {
  this.count = 0;
  document.body.addEventListener("click", () => {
    this.count++;
    console.log(this.count);
  });
}
new Counter();
```

### 🔹 객체 리터럴 반환

```javascript
const makeUser = (name, age) => ({ name, age });
```

---

## ✅ 7. 언제 사용하면 좋은가?

| 상황 | 권장 여부 |
|------|-----------|
| 짧은 콜백 함수 | O |
| 배열 메서드(map, filter, reduce) | O |
| 클래스 메서드 정의 | ❌ |
| 생성자 함수 | ❌ |
| 복잡한 비즈니스 로직 함수 | ❌ (가독성 저하) |

---

## 🎯 요약

- 간결한 문법 + this가 상위 컨텍스트 바인딩
- 익명 함수, 콜백 함수에 적합
- 클래스 메서드, 이벤트 핸들러에는 일반 함수 권장
