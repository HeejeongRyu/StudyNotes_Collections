
# 📘 JavaScript: `document.addEventListener('readystatechange', ...)` 상세 설명

---

## ✅ 1. 개념

```javascript
document.addEventListener('readystatechange', function() {
  // 실행 코드
});
```

- `readystatechange` 이벤트는 문서의 **readyState** 속성이 변경될 때마다 발생합니다.
- 문서가 로딩되는 **각 단계**를 감지하고, 특정 상태에서만 실행되도록 제어할 수 있습니다.

---

## ✅ 2. `document.readyState` 상태값

| 상태 값       | 설명 |
|---------------|------|
| `"loading"`   | HTML 문서를 불러오는 중 (아직 DOM 트리 생성 안 됨) |
| `"interactive"` | 문서가 파싱 완료됨, DOM 접근 가능 (이미지, 스타일시트 등은 아직 불러오는 중일 수 있음) |
| `"complete"`  | 모든 리소스 (이미지, 스타일시트, 스크립트 등) 불러오기 완료 |

---

## ✅ 3. 사용 예시

```javascript
document.addEventListener('readystatechange', function () {
  if (document.readyState === 'interactive') {
    console.log('DOM is ready, but resources may still be loading.');
  } else if (document.readyState === 'complete') {
    console.log('Everything is loaded.');
  }
});
```

- 로딩 도중 특정 시점에 원하는 작업을 하도록 할 수 있음

---

## ✅ 4. `readystatechange` vs `DOMContentLoaded` vs `load`

| 이벤트 | 발생 시점 | 설명 |
|--------|-----------|------|
| `readystatechange` | readyState 값이 바뀔 때마다 | 상태를 세분화해 제어 가능 |
| `DOMContentLoaded` | DOM 트리 구성 완료 시 | 이미지, CSS 등은 아직 불러올 수 있음 |
| `load` | 모든 리소스 로딩 완료 시 | 가장 마지막에 실행됨 (완전한 준비 상태) |

---

## ✅ 5. 관련 코드 예시 비교

### `DOMContentLoaded`

```javascript
document.addEventListener('DOMContentLoaded', function () {
  console.log('DOM fully loaded and parsed');
});
```

### `window.onload`

```javascript
window.onload = function () {
  console.log('Page fully loaded, including styles and images');
};
```

---

## ✅ 6. 언제 어떤 이벤트를 써야 할까?

| 상황 | 추천 이벤트 |
|------|-------------|
| DOM만 조작하면 되는 경우 | `DOMContentLoaded` |
| 이미지나 CSS까지 모두 준비 후 처리할 경우 | `window.onload` |
| 세밀하게 로딩 단계 제어가 필요할 때 | `readystatechange` |

---

## ✅ 7. 응용: 로딩 상태 표시

```javascript
document.addEventListener('readystatechange', () => {
  if (document.readyState === 'loading') {
    document.body.innerText = '로딩 중...';
  } else if (document.readyState === 'complete') {
    document.body.innerText = '완료!';
  }
});
```

---

## 📌 요약

- `readystatechange`는 `document.readyState` 값이 바뀔 때마다 발생하는 이벤트
- `loading`, `interactive`, `complete` 상태를 감지 가능
- 더 정밀한 로딩 제어가 필요할 때 유용
- 실무에서는 `DOMContentLoaded`가 가장 일반적으로 사용됨

> ⚠️ `readystatechange`는 **사용 빈도는 낮지만 제어력이 높아**, 초기 로딩 UI나 복잡한 페이지 흐름 제어에 유리합니다.
