
# 📘 Java `DecimalFormat` 사용법 + 예외 대응

`DecimalFormat`은 숫자를 포맷팅하거나 문자열을 숫자로 변환할 수 있는 Java의 유틸리티 클래스입니다.  
그러나 **null, 한글, 잘못된 문자열** 입력 시 예외가 발생하므로, 안전한 사용을 위해 예외 처리 방법도 함께 사용해야 합니다.

---

## ✅ 기본 개념

```java
DecimalFormat df = new DecimalFormat("#,##0.00");
String result = df.format(12345.678); // "12,345.68"
```

- 숫자 → 문자열: `format()`
- 문자열 → 숫자: `parse()`

---

## ✅ 주요 포맷 기호

| 기호 | 의미 |
|------|------|
| `0` | 자리수 고정, 빈 자리도 0으로 채움 |
| `#` | 자리수 표시, 빈 자리는 생략 |
| `.` | 소수점 구분 |
| `,` | 천 단위 구분 |
| `%` | 퍼센트 표시 (자동으로 100 곱함) |
| `¤` | 통화 기호 (원화, 달러 등) |

---

## ✅ 예외 발생 예시

```java
DecimalFormat df = new DecimalFormat("#,##0.00");
df.parse("한글"); // java.text.ParseException 발생
df.parse(null);   // NullPointerException 발생
```

---

## ✅ 안전한 파싱 함수 예시

```java
public static Double safeParse(String input, String pattern) {
    if (input == null || input.trim().isEmpty()) return null;

    try {
        DecimalFormat df = new DecimalFormat(pattern);
        return df.parse(input).doubleValue();
    } catch (ParseException e) {
        System.err.println("파싱 실패: " + input);
        return null;
    }
}
```

---

## ✅ 안전한 포맷 함수 예시

```java
public static String safeFormat(Number number, String pattern) {
    if (number == null) return "";

    DecimalFormat df = new DecimalFormat(pattern);
    return df.format(number);
}
```

---

## ✅ 사용 예시

```java
System.out.println(safeParse("1,234.56", "#,##0.00")); // 1234.56
System.out.println(safeParse("한글", "#,##0.00"));       // null
System.out.println(safeParse(null, "#,##0.00"));         // null

System.out.println(safeFormat(12345.678, "#,##0.00"));   // "12,345.68"
System.out.println(safeFormat(null, "#,##0.00"));        // ""
```

---

## ✅ 요약

| 기능 | 설명 |
|------|------|
| `format()` | 숫자 → 문자열 (자리수, 통화, 퍼센트 포맷) |
| `parse()` | 문자열 → 숫자 (예외 발생 가능) |
| null/한글 대응 | try-catch, null 체크로 안전하게 처리 |
| 공통 패턴 | "#,##0.00", "00000", "¤#,##0.00" 등 |

> 🎯 실무에서는 사용자 입력값이 항상 정상이 아니기 때문에 `safeParse`와 `safeFormat`을 만들어 사용하는 것이 좋습니다.
