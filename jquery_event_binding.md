# 🔄 jQuery 이벤트 바인딩 방식 비교: `on` vs `addEventListener` vs `$(document).on`

JavaScript 또는 jQuery에서 DOM 요소의 이벤트를 바인딩할 때, 특히 **동적으로 생성된 요소**에 대한 처리에서 세 가지 방식은 작동 방식에 차이가 있습니다.

---

## ✅ 1. `$('#d_no').on('change', ...)`

```javascript
$('#d_no').on('change', function() {
	alert(11);
	updateEtc_02();
});
```

- **직접 바인딩 방식**
- `#d_no` 요소가 **스크립트 실행 시점에 DOM에 존재할 경우에만** 작동
- **동적으로 생성된 요소에는 작동하지 않음**

❌ **동적 요소에는 적용되지 않음 → 작동 실패**

---

## ✅ 2. `document.getElementById(...).addEventListener(...)`

```javascript
document.getElementById('d_no').addEventListener('change', function() {
	alert(11);
});
```

- **Vanilla JS 방식**
- 마찬가지로, 해당 ID의 요소가 **이미 존재해야** 작동
- **동적 생성된 요소에는 적용되지 않음**

❌ **동적 요소에는 적용되지 않음 → 작동 실패**

---

## ✅ 3. `$(document).on('change', '#d_no', ...)`

```javascript
$(document).on('change', '#d_no', function() {
	alert(333);
});
```

- **이벤트 위임(Event Delegation)** 방식
- `#d_no`가 나중에 **AJAX 또는 자바스크립트로 동적으로 생성되어도** 이벤트가 정상 작동
- `document`는 항상 존재하므로 이벤트를 위임할 수 있음

✅ **동적 요소에도 적용 가능 → 작동 성공**

---

## 📊 작동 여부 비교표

| 방식 | DOM에 이미 존재해야 작동? | 동적 요소 대응 | 작동 여부 |
|------|-----------------------------|----------------|------------|
| `$('#d_no').on(...)` | ✅ 필요함 | ❌ 불가 | ❌ 실패 |
| `addEventListener(...)` | ✅ 필요함 | ❌ 불가 | ❌ 실패 |
| `$(document).on(...)` | ❌ 필요 없음 | ✅ 가능 | ✅ 성공 |

---

## ✅ 결론

동적으로 `<select id="d_no">` 와 같은 요소를 생성한 경우, 반드시 다음과 같이 **이벤트 위임 방식**을 사용해야 합니다:

```javascript
$(document).on('change', '#d_no', function() {
	// 동작 처리
});
```

이벤트 위임은 jQuery에서 **동적 UI 처리 시 필수적인 기법**입니다.
