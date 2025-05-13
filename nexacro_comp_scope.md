
# 📘 넥사크로 N 컴포넌트 참조 및 스코프 구조

## ✅ comp란?

넥사크로 N에서는 `comp`는 **컴포넌트 인스턴스**를 의미합니다.

- Button, Edit, Grid 등 모든 UI 컴포넌트는 인스턴스(`comp`)로 동작
- 스크립트에서 이벤트 발생 시 넘겨받는 `obj`는 `comp` 역할
- 주로 이벤트 함수에서 컴포넌트를 다룰 때 사용

```javascript
function Button00_onclick(obj:Button, e:ClickEventInfo)
{
    trace(obj.name); // 'Button00'
}
```

## ✅ parent란?

- `comp.parent` : 해당 컴포넌트가 속한 **Form 인스턴스**
- Form의 변수(`var2`) 또는 메서드(`method()`)에 접근 가능

```javascript
comp.parent.var2 = "Hello Nexacro";
```

---

## ✅ 스코프 구조

```
Application
└── MainFrame
    └── ChildFrame
        └── Form (this)
            └── Button00 (comp)
```

| 참조               | 의미 |
|------------------|------|
| `this`           | 현재 Form 인스턴스 |
| `comp` 또는 `obj` | 이벤트 발생한 컴포넌트 |
| `comp.parent`    | Form (this) |
| `application`    | 전체 Application 인스턴스 |

---

## ✅ 자주 사용하는 참조 예시

| 구문                         | 설명 |
|----------------------------|------|
| `this`                      | 현재 Form |
| `this.Button00`             | Form 안의 Button00 컴포넌트 |
| `comp.parent`               | comp의 소속 Form (this) |
| `application`               | 전체 앱 (글로벌) |
| `this.getOwnerFrame()`      | 현재 Form을 포함하는 Frame |

---

## ✅ 정리 요약

| 대상        | 설명                      |
|-----------|------------------------|
| comp      | 컴포넌트 인스턴스 (Button, Edit 등) |
| parent    | 컴포넌트의 소속 Form 인스턴스 |
| this      | 현재 Form |
| application | 글로벌 Application 인스턴스 |

---
