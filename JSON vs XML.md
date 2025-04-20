# 📦 JSON vs XML 차이점 및 관련 개념 정리

## ✅ JSON vs XML 비교표

| 항목             | JSON (JavaScript Object Notation)            | XML (eXtensible Markup Language)       |
|------------------|-----------------------------------------------|----------------------------------------|
| **구조**         | 키-값 쌍 (`{ "name": "John" }`)              | 태그 기반 (`<name>John</name>`)       |
| **가독성**       | 짧고 간결함                                   | 비교적 길고 복잡함                     |
| **데이터 용량**  | 작음 (최소한의 문법 사용)                    | 큼 (태그가 많아짐)                     |
| **파싱 속도**    | 빠름 (JS 기반으로 처리 용이)                 | 느림 (문법이 복잡)                     |
| **지원 언어**    | JavaScript에 최적화, 대부분 언어에서 지원    | 모든 언어에서 지원                     |
| **주석 가능 여부**| ❌ (표준 JSON은 주석 불가)                   | ⭕ `<-- 주석 -->` 사용 가능             |
| **데이터 타입**  | 문자열, 숫자, 배열, 객체 등 명확히 표현 가능 | 모두 문자열로 표현됨                   |
| **스키마 지원**  | 일부 툴에서 지원 (XSD만큼 정형적이지 않음)   | XSD를 통해 엄격한 스키마 정의 가능     |
| **사용 분야**    | 웹 API, AJAX, 모바일 등                      | 문서 중심 데이터, 웹서비스(SOAP 등)    |

---

## ✅ 예시 비교

### 📄 JSON 예시

```json
{
  "person": {
    "name": "John",
    "age": 30,
    "hobbies": ["reading", "traveling"]
  }
}

### 📄 XML 예시

```xml
<person>
  <name>John</name>
  <age>30</age>
  <hobbies>
    <hobby>reading</hobby>
    <hobby>traveling</hobby>
  </hobbies>
</person>
