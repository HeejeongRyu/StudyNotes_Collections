
# 🛡️ Java Directory Traversal 대응 방안

## 📌 개요

**디렉토리 트래버설(Directory Traversal)** 또는 **패스 트래버설(Path Traversal)** 취약점은 공격자가 `../` 등의 상대경로 문자를 활용해 웹 애플리케이션이 의도하지 않은 상위 디렉토리나 민감한 시스템 파일에 접근할 수 있도록 유도하는 공격입니다.

예:
```
http://example.com/download?file=../../../../etc/passwd
```

## ⚠️ 피해 사례

- 사용자 인증 없이 서버 내 중요한 설정 파일(`web.xml`, `application.properties` 등)에 접근 가능
- 로그, 인증 토큰, 비밀번호 등의 민감 정보 유출
- 웹 루트 외부의 파일을 다운로드 또는 열람

---

## 🔍 취약 코드 예시

```java
String filePath = request.getParameter("file"); // 예: ../../../../etc/passwd
File file = new File("C:/upload/" + filePath);
FileInputStream fis = new FileInputStream(file); // 위험!
```

---

## ✅ 안전한 처리 방안

### 1. **절대 경로를 이용한 기준 경로 제한**

```java
String fileRootPath = "/var/www/uploads/";
String filePath = request.getParameter("file");

Path basePath = Paths.get(fileRootPath).toAbsolutePath().normalize();
Path targetPath = basePath.resolve(filePath).normalize();

if (!targetPath.startsWith(basePath)) {
    response.sendError(HttpServletResponse.SC_FORBIDDEN, "허용되지 않은 경로입니다.");
    return;
}

File targetFile = targetPath.toFile();
if (!targetFile.exists()) {
    response.sendError(HttpServletResponse.SC_NOT_FOUND, "파일을 찾을 수 없습니다.");
    return;
}

try (InputStream in = new FileInputStream(targetFile)) {
    // 파일 스트림 처리
}
```

### 2. **Whitelist 방식으로 파일명 검증**

```java
String fileName = request.getParameter("file");

if (fileName.contains("..") || fileName.contains("/") || fileName.contains("\")) {
    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "잘못된 파일명입니다.");
    return;
}
```

### 3. **파일 확장자 제한**

```java
String ext = FilenameUtils.getExtension(fileName);
List<String> allowedExtensions = Arrays.asList("txt", "pdf", "jpg");

if (!allowedExtensions.contains(ext)) {
    response.sendError(HttpServletResponse.SC_FORBIDDEN, "허용되지 않은 파일 형식입니다.");
    return;
}
```

---

## 🔒 추가 보안 권장사항

- 업로드/다운로드 파일은 외부 접근이 불가능한 디렉토리에 저장하고, 서블릿을 통해 제어
- `Canonical Path` 비교를 통해 파일 경로 변조 방지
- 로깅 및 이상 접근 탐지를 위한 모니터링 도입

---

## 🧪 테스트 방법

- 공격 벡터: `../../`, `%2e%2e%2f`, `%c0%ae%c0%ae%c0%af` 등 인코딩된 패턴을 통한 우회 시도
- Burp Suite, OWASP ZAP 등의 도구를 활용한 입력 값 fuzzing
- 운영 환경에서 접근 로그를 통한 비정상 경로 탐지

---

## 🛠️ 참고 링크

- [OWASP Directory Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [Spring Security File Access Guide](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-authorization)

---

## 🧾 결론

디렉토리 트래버설은 단순하지만 매우 치명적인 취약점입니다. 사용자 입력값을 절대 신뢰하지 말고, 경로 정규화(normalization)와 루트 디렉토리 제한, 화이트리스트 검증 등을 통해 안전한 파일 접근을 구현해야 합니다. 보안을 고려한 설계와 지속적인 테스트가 필수입니다.
