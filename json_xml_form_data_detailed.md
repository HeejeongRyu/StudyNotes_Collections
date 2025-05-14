
# 📘 JSON, XML, Form Data 데이터 타입 상세 설명

---

## ✅ 1. JSON (JavaScript Object Notation)

### 🔹 정의
- 경량의 데이터 교환 형식
- 텍스트 기반이며, JavaScript 객체 문법을 따름
- 오늘날 API 통신 표준 형식

### 🔹 구조 예시

```json
{
  "name": "홍길동",
  "age": 30,
  "skills": ["JavaScript", "Python"],
  "address": {
    "city": "Seoul",
    "zip": "12345"
  }
}
```

### 🔹 특징

| 항목 | 설명 |
|------|------|
| 경량성 | 가볍고, 사람이 읽기 쉬움 |
| 키-값 쌍 구조 | JavaScript 객체와 유사 |
| 지원 언어 | 거의 모든 언어 지원 (파싱/생성 쉽게 가능) |
| 확장성 | 배열, 객체 중첩 가능 |

### 🔹 사용 예
- REST API 응답 (일반적)
- 모바일 앱 - 서버 간 통신
- 웹 프론트엔드와 백엔드 통신

### 🔹 장점
- 직관적, 가독성 높음
- 파싱, 직렬화 속도 빠름
- 브라우저 기본 지원

### 🔹 단점
- 메타데이터 부족 (스키마 없음)
- 타입 명시 부족
- 바이너리 데이터 전달에 부적합

---

## ✅ 2. XML (eXtensible Markup Language)

### 🔹 정의
- 태그 기반의 데이터 표준 포맷
- 데이터 + 메타데이터 표현 가능
- B2B, 금융, 레거시 시스템에서 여전히 사용

### 🔹 구조 예시

```xml
<user>
  <name>홍길동</name>
  <age>30</age>
  <skills>
    <skill>JavaScript</skill>
    <skill>Python</skill>
  </skills>
  <address city="Seoul" zip="12345"/>
</user>
```

### 🔹 특징

| 항목 | 설명 |
|------|------|
| 태그 기반 | HTML과 유사한 구조 |
| 메타데이터 표현 가능 | 속성, 네임스페이스, 스키마 지원 |
| 확장성 | 사용자 정의 태그, 스키마 정의 가능 |
| 호환성 | 금융, 정부, 공공 시스템에서 선호 |

### 🔹 사용 예
- SOAP Web Service
- 전자문서 표준
- 금융, 정부 시스템 연계

### 🔹 장점
- 엄격한 구조, 스키마 검증 가능
- 메타데이터, 다국어, 명세화 용이
- 이진 데이터(Base64) 포함 가능

### 🔹 단점
- 구문이 복잡, 무겁고 가독성 떨어짐
- 파싱 느림
- JSON에 비해 모바일/웹에서 비효율적

---

## ✅ 3. Form Data

### 🔹 정의
- HTML `<form>` 태그에서 전송되는 표준 데이터 형식
- 서버에서 웹 폼 데이터 수신 시 표준
- 텍스트 키=값 구조 또는 파일 업로드 지원

### 🔹 구조 예시

#### application/x-www-form-urlencoded

```
name=홍길동&age=30&skills=JavaScript&skills=Python
```

#### multipart/form-data

```
------WebKitFormBoundary
Content-Disposition: form-data; name="name"

홍길동
------WebKitFormBoundary
Content-Disposition: form-data; name="profile"; filename="profile.jpg"
Content-Type: image/jpeg

(바이너리 파일 데이터)
------WebKitFormBoundary--
```

### 🔹 특징

| 타입 | 설명 |
|------|------|
| application/x-www-form-urlencoded | URL 인코딩, 텍스트 데이터 |
| multipart/form-data | 경계로 구분, 파일 업로드 지원 |

### 🔹 사용 예
- HTML `<form>` POST 요청
- 파일 업로드
- 기존 서버 (PHP, JSP) 연동

### 🔹 장점
- 서버 호환성 높음
- 파일 업로드 가능
- 간단한 키=값 구조는 가볍고 빠름

### 🔹 단점
- JSON과 비교 시 구조화 데이터 표현 어려움
- 중첩 객체, 배열 표현 불편
- 클라이언트에서 JSON보다 작성 복잡

---

## ✅ 4. JSON vs XML vs Form Data 비교

| 항목 | JSON | XML | Form Data |
|------|------|-----|-----------|
| 형식 | 키-값 쌍 | 태그 기반 | 키=값 or multipart |
| 가독성 | 우수 | 복잡 | 간단 |
| 확장성 | 배열, 객체 자유롭게 중첩 | 스키마, 네임스페이스 활용 | 복잡한 구조 표현 어려움 |
| 데이터 타입 명확성 | 낮음 | 높음 | 낮음 |
| 메타데이터 | 없음 | 가능 | 없음 |
| 이진 데이터 | Base64 별도 처리 필요 | 가능 (Base64) | multipart/form-data로 가능 |
| 사용 분야 | API, 모바일, 웹 | 금융, 공공, B2B | HTML 폼, 파일 업로드 |

---

## ✅ 5. 실무 권장

| 사용 상황 | 권장 타입 |
|-----------|------------|
| REST API 통신 | JSON |
| 레거시 시스템 연계 | XML |
| HTML 폼 제출, 파일 업로드 | Form Data |
| IoT, 경량 통신 | JSON |
| 문서화, 데이터 스키마 검증 필요 | XML |

---

## 🎯 마무리

- JSON: 가볍고 직관적, REST API 표준
- XML: 엄격하고 확장성 높음, 레거시 시스템 선호
- Form Data: 웹 폼, 파일 업로드 표준

> 현대 웹, 모바일에서는 JSON + Form Data 혼합 사용이 일반적이며, XML은 특수 산업군에서 계속 사용됩니다.
