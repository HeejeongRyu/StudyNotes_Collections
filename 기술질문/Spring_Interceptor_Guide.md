# 🧭 Spring MVC Interceptor 완전 정복

## 1) 인터셉터란?

**Spring MVC의 `HandlerInterceptor`** 는 요청이 **컨트롤러(핸들러)** 에 도달하기 전/후와 **뷰 렌더링 이후** 지점에 개입하여 공통 로직을 실행할 수 있게 하는 컴포넌트입니다.  
Servlet 레벨의 `Filter` 보다 **MVC 흐름에 더 가까운 위치**에서 동작하며, 주로 다음에 쓰입니다.

- 로그인/권한 체크(간단한 수준)  
- 요청/응답 **로깅 & 트레이싱(MDC, requestId)**  
- 다국어/테넌트/컨텍스트 값 주입  
- API 버전/헤더 정책 검사  
- 성능 측정(핸들러 기준)  
- 모델 공통 속성 주입(뷰 기반)

> 💡 **핵심 포인트**  
> - 실행 위치: **HandlerMapping → Interceptor(preHandle) → Controller → Interceptor(postHandle) → View → Interceptor(afterCompletion)**  
> - **`preHandle`에서 `false`를 리턴하면** 컨트롤러로 진행하지 않고 **요청을 즉시 종료**할 수 있습니다(단, 그 인터셉터의 `afterCompletion`은 호출되지 않음).

---

## 2) 처리 파이프라인에서의 위치

```
Client
  ↳ (Servlet Filter Chain)      ← 서블릿/보안/인코딩/CORS 등
     ↳ DispatcherServlet
        ↳ HandlerMapping
           ↳ Interceptor.preHandle(...)
              ↳ Controller(Handler) 실행
           ↳ Interceptor.postHandle(..., ModelAndView)
        ↳ View Rendering
     ↳ Interceptor.afterCompletion(..., Exception)
```

- `Filter`는 **서블릿 컨테이너 레벨**에서,  
- `Interceptor`는 **Spring MVC 레벨**에서,  
- `AOP`는 **스프링 빈 메서드 호출 레벨**에서 개입합니다.

---

## 3) 인터페이스와 생명주기

```java
public interface HandlerInterceptor {

    default boolean preHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler
    ) throws Exception {
        return true; // false 반환 시 체인 중단(컨트롤러 미진입)
    }

    default void postHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        @Nullable ModelAndView modelAndView
    ) throws Exception {}

    default void afterCompletion(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        @Nullable Exception ex
    ) throws Exception {}
}
```

### 비동기 요청(DeferredResult/Callable) 처리
비동기 컨트롤러를 쓰면 **호출 스레드가 분리**될 수 있습니다. 이때는 `AsyncHandlerInterceptor`의 훅을 활용합니다.

```java
public interface AsyncHandlerInterceptor extends HandlerInterceptor {
    default void afterConcurrentHandlingStarted(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler
    ) throws Exception {}
}
```

- `afterConcurrentHandlingStarted`: 비동기 처리로 **컨트롤러 반환 직후**(아직 결과 완료 전) 호출
- 이후 결과가 완료되면 **별도 스레드에서** `preHandle` 없이 `postHandle`/`afterCompletion`이 재호출될 수 있습니다.

---

## 4) 등록 방법 (Spring Boot / Spring 6+)

가장 일반적인 방법은 `WebMvcConfigurer#addInterceptors`.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        registry.addInterceptor(new LoggingInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns(
                    "/css/**", "/js/**", "/images/**", "/favicon.ico", "/error"
                )
                .order(1);

        registry.addInterceptor(new AuthInterceptor())
                .addPathPatterns("/api/**")
                .excludePathPatterns("/api/public/**")
                .order(2);
    }
}
```

> 🧩 **경로 매칭**  
> - Spring 6+/Boot 3+ 기본은 **PathPatternParser**이며, 과거 `AntPathMatcher`와 문법이 약간 다릅니다.  
> - 복잡한 패턴은 단순화하고, 정적 리소스는 꼭 `excludePathPatterns`로 제외하세요.

### Bean으로 범위 지정: `MappedInterceptor`
```java
@Bean
public MappedInterceptor apiMappedInterceptor(AuthInterceptor authInterceptor) {
    return new MappedInterceptor(
        new String[]{"/api/**"},          // include
        new String[]{"/api/public/**"},   // exclude
        authInterceptor
    );
}
```

---

## 5) 실전 예제

### 5.1 요청 로깅 & 처리 시간 측정 (MDC 포함)

```java
@Slf4j
public class LoggingInterceptor implements HandlerInterceptor {

    private static final String START = "requestStartTime";
    private static final String REQ_ID = "reqId";

    @Override
    public boolean preHandle(HttpServletRequest req, HttpServletResponse res, Object handler) {
        String requestId = Optional.ofNullable(req.getHeader("X-Request-Id"))
                                   .orElse(UUID.randomUUID().toString());
        MDC.put(REQ_ID, requestId);
        req.setAttribute(START, System.currentTimeMillis());

        String uri = req.getRequestURI();
        String method = req.getMethod();

        if (handler instanceof HandlerMethod hm) {
            log.info("[{}] {} {} -> {}#{}()",
                requestId, method, uri,
                hm.getBeanType().getSimpleName(), hm.getMethod().getName());
        } else {
            log.info("[{}] {} {} -> handler: {}", requestId, method, uri, handler);
        }
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest req, HttpServletResponse res, Object handler, Exception ex) {
        Long start = (Long) req.getAttribute(START);
        long took = (start != null) ? (System.currentTimeMillis() - start) : -1L;

        int status = res.getStatus();
        if (ex != null) {
            log.error("[{}] completed {}ms status={} ex={}", MDC.get(REQ_ID), took, status, ex.toString(), ex);
        } else {
            log.info("[{}] completed {}ms status={}", MDC.get(REQ_ID), took, status);
        }
        MDC.remove(REQ_ID);
    }
}
```

```java
@Bean
public TaskDecorator mdcTaskDecorator() {
    return runnable -> {
        var contextMap = MDC.getCopyOfContextMap();
        return () -> {
            if (contextMap != null) MDC.setContextMap(contextMap);
            try { runnable.run(); }
            finally { MDC.clear(); }
        };
    };
}
```

### 5.2 간단 인증/권한 체크

```java
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest req, HttpServletResponse res, Object handler) throws Exception {
        Object loginUser = req.getSession(false) == null ? null :
                           req.getSession(false).getAttribute("LOGIN_USER");

        String apiKey = req.getHeader("X-API-KEY");

        boolean ok = (loginUser != null) || "my-api-key-123".equals(apiKey);
        if (!ok) {
            res.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            res.setContentType("application/json;charset=UTF-8");
            res.getWriter().write("{"message":"Unauthorized"}");
            return false;
        }
        return true;
    }
}
```

### 5.3 모델 공통 속성 주입(뷰 기반)

```java
public class CommonModelInterceptor implements HandlerInterceptor {
    @Override
    public void postHandle(
        HttpServletRequest req, HttpServletResponse res, Object handler, ModelAndView mav) {

        if (mav != null && !isRestResponse(handler)) {
            mav.addObject("serviceName", "Order Service");
            mav.addObject("serverTime", LocalDateTime.now());
        }
    }

    private boolean isRestResponse(Object handler) {
        if (handler instanceof HandlerMethod hm) {
            return hm.getBeanType().isAnnotationPresent(RestController.class)
                || hm.getMethod().isAnnotationPresent(ResponseBody.class);
        }
        return false;
    }
}
```

---

## 6) 핸들러 정보 활용(어노테이션/메타데이터)

```java
@Override
public boolean preHandle(HttpServletRequest req, HttpServletResponse res, Object handler) {
    if (handler instanceof HandlerMethod hm) {
        Method method = hm.getMethod();
        Class<?> controller = hm.getBeanType();

        ApiPolicy policy = AnnotatedElementUtils.findMergedAnnotation(method, ApiPolicy.class);
        if (policy != null && policy.requireTenant()) {
            String tenant = req.getHeader("X-Tenant-Id");
            if (tenant == null) {
                res.sendError(400, "X-Tenant-Id required");
                return false;
            }
        }
    }
    return true;
}
```

---

## 7) 여러 인터셉터 체인 & 순서

- 등록 시 `order(n)`으로 **낮을수록 먼저** 실행됩니다.
- 실행/해제 규칙
  - `preHandle`은 **등록 순서대로** 진행
  - `postHandle`/`afterCompletion`은 **역순**으로 호출
  - 어떤 인터셉터가 `preHandle=false`면 **그 이후 인터셉터/컨트롤러는 실행되지 않음**
  - `afterCompletion`은 **`preHandle=true`였던 인터셉터들만** 호출

---

## 8) 예외 처리와의 관계

- 컨트롤러나 뷰 단계에서 예외가 발생해도 **`afterCompletion`** 이 호출되어 정리를 보장합니다(해당 인터셉터가 `preHandle=true`였을 때).
- 공통 예외 응답 포맷은 **`@ControllerAdvice`** 로 처리하고, 인터셉터에서는 **최소한의 포맷팅/로깅**만 수행하는 편이 안정적입니다.

---

## 9) Filter / Interceptor / AOP 비교

| 구분 | Filter | Interceptor | AOP |
|---|---|---|---|
| 레벨 | Servlet | Spring MVC | Spring Bean |
| 트리거 | URL, 모든 리소스 | 핸들러 호출 전후/완료 | 메서드 실행 전후/예외 |
| 주용도 | 인코딩, CORS, 보안체인 | 인증 보조, 로깅, 모델/정책 | 트랜잭션, 로깅, 규칙형 단면 |
| 요청 바디 접근 | 가능(주의 필요) | **어려움**(바디 가공은 `ResponseBodyAdvice` 권장) | 메서드 파라미터/리턴 중심 |
| 보안 | 강함(경계) | 보조적 | 보조적 |

---

## 10) 테스트(예: MockMvc)

```java
@WebMvcTest(controllers = OrderController.class)
@Import({WebConfig.class, LoggingInterceptor.class})
class OrderControllerTest {

    @Autowired MockMvc mvc;

    @Test
    void testOrder() throws Exception {
        mvc.perform(get("/api/orders/1"))
           .andExpect(status().isOk());
    }
}
```

---

## 11) 실무 팁 & 모범 사례

- **정적 리소스/헬스체크** 등은 반드시 `excludePathPatterns` 해 오버헤드 방지
- **MDC/ThreadLocal 정리** 필수 (`afterCompletion`에서 `remove/clear`)
- **비동기 컨트롤러** 사용 시 **컨텍스트 전파(TaskDecorator)** 설정
- **응답 바디 변형**은 인터셉터 대신 `ResponseBodyAdvice` 사용
- **보안 경계**는 Spring Security에 맡기고 인터셉터는 보완적 검증/로깅
- **경로 매칭**은 단순하게, 인터셉터는 **도메인 비즈니스 최소화**
- 공통 정책은 **어노테이션+인터셉터** 조합으로 명시적 관리

---

## 12) 자주 겪는 문제

- `preHandle=false` 반환 후 커넥션/스트림 **미정리** → 반드시 종료 응답 작성
- `MDC` 누수로 **다른 요청 로그에 섞임** → `MDC.clear()` 필수
- `@RestController` 인데 `postHandle`에서 `ModelAndView` 조작 시도 → `modelAndView`는 `null`일 수 있음
- **응답 본문 압축/캐시**와 로깅을 함께 할 때 스트림 재사용 이슈 → 별도 로깅 전략 사용