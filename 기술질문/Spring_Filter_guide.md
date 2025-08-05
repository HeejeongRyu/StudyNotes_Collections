# 🌐 Spring에서 Filter란?

## 🔍 개요

`Filter`는 **Java Servlet Specification**에서 정의된 컴포넌트로, **클라이언트의 요청과 응답을 가로채서 사전/사후 처리**를 할 수 있도록 설계된 기능입니다.  

Spring Framework에서는 이 필터를 활용해 다음과 같은 작업을 할 수 있습니다:

- ✅ **보안 검사 (Security Check)**
- ✅ **요청 및 응답 로깅**
- ✅ **공통 인코딩 처리 (ex. UTF-8)**
- ✅ **IP 제한, 세션 검증 등**
- ✅ **CORS 처리**
- ✅ **XSS 방어 로직**

> 🔧 핵심 특징은 `DispatcherServlet` **이전에** 실행된다는 점입니다.  
> 즉, **Spring MVC의 Controller가 호출되기 전**에 요청을 처리하거나 필터링할 수 있습니다.

---

## 🧩 Filter의 동작 위치

```
[Client] → [Filter Chain] → [DispatcherServlet] → [Controller]
```

`Filter`는 Servlet Container 레벨에서 동작하며, **`DispatcherServlet`보다 먼저 요청을 가로챕니다.**  
즉, Spring의 전통적인 AOP나 Interceptor보다도 더 앞단에서 작동합니다.

---

## 🛠 Filter 인터페이스 구조

```java
public interface Filter {
    void init(FilterConfig filterConfig) throws ServletException;

    void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException;

    void destroy();
}
```

| 메서드 | 설명 |
|--------|------|
| `init()` | 필터 초기화 시 호출 |
| `doFilter()` | 요청/응답 처리 로직을 구현하는 핵심 메서드 |
| `destroy()` | 필터 종료 시 리소스 정리 등 수행 |

---

## 📦 실습 예제: 요청 로그 필터

### 1. Java 클래스로 필터 구현

```java
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;
import java.util.Date;

@WebFilter("/*") // 모든 요청에 대해 적용
public class LoggingFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("[LoggingFilter] 초기화");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        long start = System.currentTimeMillis();

        System.out.println("[LoggingFilter] 요청이 들어왔습니다. 시간: " + new Date());

        // 다음 필터 또는 서블릿으로 요청 전달
        chain.doFilter(request, response);

        long end = System.currentTimeMillis();
        System.out.println("[LoggingFilter] 요청 처리 완료. 소요 시간: " + (end - start) + "ms");
    }

    @Override
    public void destroy() {
        System.out.println("[LoggingFilter] 종료");
    }
}
```

### 2. 필터 등록 방법

#### ✅ 방법 A: `@WebFilter` + `@ServletComponentScan`

```java
// Application 클래스
@SpringBootApplication
@ServletComponentScan  // 필터, 서블릿, 리스너 자동 스캔
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

#### ✅ 방법 B: 수동 등록 (`FilterRegistrationBean` 사용)

```java
@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean<LoggingFilter> loggingFilter() {
        FilterRegistrationBean<LoggingFilter> registrationBean = new FilterRegistrationBean<>();

        registrationBean.setFilter(new LoggingFilter());
        registrationBean.addUrlPatterns("/*"); // 모든 요청에 대해 필터 적용
        registrationBean.setOrder(1); // 필터 순서 설정 (낮을수록 먼저 실행)

        return registrationBean;
    }
}
```

---

## ⚖️ Filter vs Interceptor vs AOP

| 항목 | Filter | Interceptor | AOP |
|------|--------|-------------|-----|
| 위치 | DispatcherServlet 이전 | DispatcherServlet 이후 | 스프링 빈 내부 메서드 |
| 용도 | 인증, 로깅, 인코딩 | 로그인 인증, 권한 처리 | 트랜잭션, 로깅, 예외 처리 |
| 대상 | ServletRequest / Response | Handler (Controller) | Bean의 메서드 |
| 라이프사이클 | Servlet 스펙에 따름 | Spring MVC 흐름에 따름 | Spring 컨테이너에 따름 |

---

## 📌 Filter 사용 시 주의사항

- 요청/응답을 조작할 때는 **Stream을 한 번만 읽을 수 있다는 점**에 유의해야 합니다.
- 요청에 대한 정보를 다시 전달해야 하는 경우에는 `HttpServletRequestWrapper`, `HttpServletResponseWrapper`를 사용해야 합니다.
- 여러 개의 필터를 사용하는 경우, `order` 또는 `FilterChain`의 순서를 명확히 제어해야 합니다.

---

## 🧪 실습 예제: 인코딩 필터

```java
public class EncodingFilter implements Filter {

    private String encoding = "UTF-8";

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        request.setCharacterEncoding(encoding);
        response.setCharacterEncoding(encoding);

        chain.doFilter(request, response); // 다음 필터/서블릿 호출
    }
}
```

---

## 🧠 마무리 요약

- `Filter`는 **서블릿 기반에서 요청/응답을 전처리/후처리** 할 수 있는 강력한 도구입니다.
- 보안, 로깅, 인코딩 같은 **전역적인 관심사 처리에 적합**합니다.
- Spring에서는 `FilterRegistrationBean` 또는 `@WebFilter`를 통해 등록할 수 있습니다.
- `Filter → Interceptor → AOP` 순으로 더 세밀한 처리가 가능합니다.

---

## 📚 참고 자료

- [Servlet 공식 문서](https://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html)
- [Spring 공식 문서 - 서블릿 필터](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-embedded-container-servlets-filters-listeners)