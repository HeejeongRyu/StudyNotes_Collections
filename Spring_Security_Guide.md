# Spring Security 완벽 정리

---

## 1. Spring Security란?

- Spring Security는 Java/Spring 웹 애플리케이션에 **인증(Authentication)**, **인가(Authorization)**, **보안 위협 방어** 기능을 쉽고 강력하게 적용할 수 있게 해주는 **표준 보안 프레임워크**입니다.
- 로그인, 로그아웃, 회원가입, 권한별 접근 제어, 패스워드 암호화, 세션/쿠키/토큰, REST API 인증, 소셜 로그인(OAuth2), 자동 로그인, CSRF 등 보안 전반을 책임짐.

---

## 2. Spring Security 주요 개념

- **인증(Authentication):** 사용자의 신원 확인 (로그인)
- **인가(Authorization):** 인증된 사용자가 특정 자원, URL, 기능에 접근할 권한이 있는지 판단
- **세션/쿠키/토큰 관리:** 로그인 후 인증 상태를 유지
- **비밀번호 암호화:** BCrypt 등으로 안전하게 저장
- **보안 위협 방어:** CSRF, 세션 고정, 일부 XSS 대응, CORS 등

---

## 3. 동작 원리 및 구조

1. **FilterChain**  
   - 모든 HTTP 요청이 Spring Security의 여러 **필터(Filter)**를 거치면서 인증/인가/세션/예외처리 등을 수행
2. **AuthenticationManager & Provider**  
   - 인증 요청 시 AuthenticationManager가 다양한 AuthenticationProvider(예: DB, LDAP, 소셜, JWT 등)에 인증 위임
3. **UserDetailsService**  
   - 사용자 정보(아이디, 비밀번호, 권한 등) 조회 및 반환
4. **SecurityContextHolder**  
   - 인증 성공 시 인증정보(Authentication)를 세션/토큰 등으로 관리
5. **Authorization**  
   - URL, 메서드, 리소스 접근 시 권한 검사

---

## 4. 주요 컴포넌트

| 컴포넌트                   | 설명                                        |
|---------------------------|--------------------------------------------|
| SecurityFilterChain       | 요청을 가로채는 필터의 집합                 |
| AuthenticationManager     | 인증(로그인) 처리 총괄                       |
| AuthenticationProvider    | 실제 인증 로직(DB, LDAP, 소셜 등)           |
| UserDetailsService        | 사용자 정보(UserDetails) 조회               |
| UserDetails               | 인증에 사용되는 사용자 정보 객체             |
| PasswordEncoder           | 패스워드 암호화/검증                        |
| GrantedAuthority          | 권한(역할) 정보                             |
| SecurityContextHolder     | 현재 인증/인가 정보 저장소                  |
| AccessDecisionManager     | 인가(접근 허용/차단) 결정                   |

---

## 5. 동작 흐름 예시 (폼 로그인)

[1] 사용자가 /login 폼 제출  
   ↓  
[2] UsernamePasswordAuthenticationFilter가 요청 가로챔  
   ↓  
[3] AuthenticationManager → AuthenticationProvider  
   ↓  
[4] UserDetailsService에서 사용자 정보 조회, 패스워드 확인  
   ↓  
[5] 인증 성공 → SecurityContextHolder에 인증 정보 저장 → 세션 생성  
   ↓  
[6] 인증 실패 → 에러 메시지 전달, 로그인 페이지로 이동  

---

## 6. Spring Security 설정/커스터마이징 (Spring Boot 기준)

### 1) 의존성 추가

```gradle
implementation 'org.springframework.boot:spring-boot-starter-security'
```

### 2) 기본 동작

- 별도 설정 없이도 `/login`, `/logout` URL이 자동 생성, 기본 로그인 폼 제공
- 모든 URL 인증 필요 상태

### 3) 사용자 정의 보안 설정 (Java Config 예시)

```java
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().permitAll()
            )
            .formLogin(form -> form
                .loginPage("/login")
                .defaultSuccessUrl("/")
                .permitAll()
            )
            .logout(logout -> logout
                .logoutUrl("/logout")
                .logoutSuccessUrl("/")
                .permitAll()
            )
            .csrf(Customizer.withDefaults());
        return http.build();
    }
}
```

### 4) UserDetailsService 구현

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    @Autowired
    private UserRepository userRepository;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new UsernameNotFoundException("User not found"));
        return new org.springframework.security.core.userdetails.User(
            user.getUsername(),
            user.getPassword(),
            AuthorityUtils.createAuthorityList(user.getRole())
        );
    }
}
```

### 5) PasswordEncoder 등록

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

## 7. 실무 주요 확장/기능

- **권한별 접근제한**  
  `.antMatchers("/admin/**").hasRole("ADMIN")`,  
  `@PreAuthorize("hasRole('ADMIN')")`
- **자동 로그인(remember-me)**  
  `.rememberMe()`
- **REST API, JWT, OAuth2(소셜 로그인)**  
  필터체인 확장/커스텀
- **예외 처리/커스텀 로그인/로그아웃 핸들러**  
- **XSS/CSRF/CORS 대응**  
  `.csrf().cors()` 등

---

## 8. 보안 기능 요약

| 기능            | 설명                                             |
|-----------------|--------------------------------------------------|
| CSRF 방어       | 폼/REST 요청 위조 차단, 자동 토큰 검증           |
| XSS (일부)      | 일부 필터링, 그러나 출력 이스케이프는 직접 구현   |
| 세션 고정 방지  | 로그인 시 세션 아이디 변경                       |
| 패스워드 암호화 | BCrypt 등 강력한 암호화 지원                     |
| CORS 대응       | 외부 도메인 요청 제어                            |
| 다양한 인증 방식| 폼 로그인, HTTP Basic, JWT, OAuth2 등            |

---

## 9. 실무 사용 팁

- **패스워드 평문 저장 금지!**  
  항상 암호화(BCrypt 등)
- **UserDetailsService로 DB/외부 연동**
- **모든 URL/메서드에 인가(권한제어) 정책 명확히**
- **프론트 출력 시 XSS 방지(escape) 추가 구현 필요**
- **CSRF, 세션 고정, CORS 등 기본 보안 설정 활용**
- **Spring Security 디버깅/로그 적극 활용**

---

## 10. 결론

- Spring Security는 Spring 애플리케이션의 **표준 보안 솔루션**  
- 인증, 인가, 최신 보안 위협 대응, REST/SPA/소셜 등 모든 보안에 활용  
- 직접 보안 코딩 거의 불필요(설정과 확장으로 안전하게 적용)
- 커스텀 필터, 인증, 인가, 핸들러 등 확장도 매우 유연

---

## 참고 자료

- [공식 문서](https://docs.spring.io/spring-security/site/docs/current/reference/html5/)
- [Spring Guides: Securing Web](https://spring.io/guides/gs/securing-web/)

---

더 궁금한 점(커스텀 인증, JWT, OAuth2, XSS 실전 대응 등) 있으면 언제든 질문 주세요!
