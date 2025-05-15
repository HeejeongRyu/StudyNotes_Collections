
# 📘 MVC 패턴 (Model-View-Controller) 상세 설명

---

## ✅ 1. MVC 패턴이란?

**MVC**는 **Model(모델), View(뷰), Controller(컨트롤러)**의 약자로,  
애플리케이션을 다음과 같이 세 가지 역할로 나눠서 관리하는 **구조적 설계 방법론**입니다.

| 구성 요소 | 역할 |
|------------|----------------------|
| Model | 데이터, 비즈니스 로직 관리 |
| View | 사용자 인터페이스(UI) |
| Controller | 사용자의 입력 처리, Model과 View 연결 |

---

## ✅ 2. MVC 패턴의 목적

| 목적 | 설명 |
|------|------|
| 관심사의 분리(SoC) | 로직과 UI를 분리하여 코드 가독성 향상 |
| 유지보수 용이 | UI 변경 시 비즈니스 로직 영향 최소화 |
| 테스트 용이 | Model, Controller 독립 테스트 가능 |
| 역할 명확화 | 각 레이어가 책임을 구분 |

---

## ✅ 3. MVC 구성 요소 상세

### 🔹 Model (모델)

```java
public class UserModel {
    private String name;
    private String email;
    // getter, setter
}
```

### 🔹 View (뷰)

```html
<h1>사용자 이름: ${user.name}</h1>
```

### 🔹 Controller (컨트롤러)

```java
@RequestMapping("/user")
public String getUser(Model model) {
    User user = userService.getUser();
    model.addAttribute("user", user);
    return "userView";
}
```

---

## ✅ 4. MVC 데이터 흐름 예시

```
[사용자 브라우저] → 클릭/입력 → [Controller] → [Model] → [Controller] → [View] → 사용자에게 응답
```

---

## ✅ 5. 실무 MVC 패턴 사례

| 프레임워크 | 구조 |
|------------|------|
| Spring MVC (Java) | Controller → Service → DAO → View |
| Django (Python) | View (Controller 역할) → Model → Template |
| ASP.NET MVC | Controller → Model → View |
| Rails (Ruby) | Controller → Model → View |

---

## ✅ 6. MVC의 장단점

### 🔹 장점

| 항목 | 설명 |
|------|------|
| 관심사 분리 | UI, 로직, 입력 처리 독립 |
| 유지보수 쉬움 | UI 변경 시 Model 영향 없음 |
| 테스트 편리 | Model, Controller 독립 테스트 가능 |
| 재사용성 높음 | Model 재사용 용이 |

### 🔹 단점

| 항목 | 설명 |
|------|------|
| 구조 복잡 | 작은 프로젝트에는 과잉 설계 가능성 |
| Controller 집중화 | 복잡한 입력 흐름 → Controller 비대해짐 |
| View와 Controller 의존성 발생 가능 | 불필요한 의존 발생 가능 |

---

## ✅ 7. MVC 패턴의 진화 (변형)

| 패턴 | 특징 |
|------|------|
| MVP | View가 Presenter에 의존 |
| MVVM | View와 ViewModel 데이터 바인딩 |
| Clean Architecture | 레이어 분리 + 의존성 역전 원칙 |

---

## ✅ 8. MVC vs MVVM vs MVP 요약

| 패턴 | View → | ↔ | Model |
|------|--------|----|-------|
| MVC | Controller | → | Model |
| MVP | Presenter | → | Model |
| MVVM | ViewModel (바인딩) | ↔ | Model |

---

## 🎯 마무리 요약

- **MVC 패턴**: 사용자의 입력, 로직, UI를 각각 **Controller, Model, View로 분리**
- **실무 표준 아키텍처**
- **유지보수, 테스트, 확장성 향상**
- **Controller 집중화 방지 및 View-Model 의존 줄이는 구조적 고민 필요**
