
# 📘 팰린드롬(Palindrome) 알고리즘 상세 설명

---

## ✅ 1. 팰린드롬이란?

> **팰린드롬(Palindrome)**은 **앞에서 읽든, 뒤에서 읽든 동일한 문자열**을 말합니다.  
> 대표적인 예시: `"level"`, `"radar"`, `"12321"`, `"기러기"` 등

---

## ✅ 2. 팰린드롬 확인 기본 아이디어

1. **양쪽에서 문자 비교** → 앞/뒤 포인터를 이용
2. **문자열을 뒤집어서 비교** → Python: `s == s[::-1]`
3. **스택/큐 이용** → 좌우 대칭 구조 활용

---

## ✅ 3. Python 기본 구현 예시

```python
def is_palindrome(s):
    return s == s[::-1]

print(is_palindrome("racecar"))  # True
print(is_palindrome("hello"))    # False
```

---

## ✅ 4. 투 포인터 방식

### 설명
- 시작 포인터(`left`)와 끝 포인터(`right`)를 이동하며 비교
- 다르면 False, 끝까지 가면 True

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

---

## ✅ 5. 응용: 문자열 내 최장 팰린드롬 부분 문자열 찾기

### 예시 문제
- 문자열 `"babad"` → 결과: `"bab"` 또는 `"aba"`

### 중심 확장(Center Expansion) 방식

```python
def expand_center(s, left, right):
    while left >= 0 and right < len(s) and s[left] == s[right]:
        left -= 1
        right += 1
    return s[left + 1:right]

def longest_palindrome(s):
    result = ""
    for i in range(len(s)):
        # 홀수 길이
        temp1 = expand_center(s, i, i)
        # 짝수 길이
        temp2 = expand_center(s, i, i + 1)
        result = max(result, temp1, temp2, key=len)
    return result
```

---

## ✅ 6. 시간 복잡도

| 문제 유형 | 알고리즘 | 시간 복잡도 |
|-----------|-----------|---------------|
| 팰린드롬 여부 확인 | 투 포인터 | O(n) |
| 최장 팰린드롬 부분 문자열 | 중심 확장 | O(n²) |
| 최장 팰린드롬 (고급) | Manacher’s Algorithm | O(n) |

---

## ✅ 7. Manacher’s Algorithm (마나커 알고리즘)

> 최장 팰린드롬 부분 문자열을 **O(n)** 시간 내에 찾는 고급 알고리즘

- 문자열을 `#`로 감싸서 짝/홀수 처리를 동일하게
- 회문 반경(Radius)을 계산하며 확장

### 개념 요약

```text
"abba" → "#a#b#b#a#"
→ 길이 2n + 1
→ 중심점 기준으로 회문 길이 p[i] 계산
```

> 마나커 알고리즘은 복잡도와 구현 난이도 때문에 **대회나 고난도 알고리즘 문제에서 사용**

---

## ✅ 8. 응용 분야

- 회문 문자열 검사기
- 텍스트 자동 보정
- 바이오 시퀀스 비교 (DNA → 회문형태 있음)
- 데이터 압축 및 복원

---

## ✅ 9. 예외 처리 시 고려사항

- 대소문자 구분 → `s.lower()`
- 공백 제거 → `s.replace(" ", "")`
- 특수문자 제거 → `re.sub(r'[^a-z0-9]', '', s.lower())`

```python
import re
def clean_and_check(s):
    s = re.sub(r'[^a-z0-9]', '', s.lower())
    return s == s[::-1]
```

---

## 🎯 마무리 요약

| 항목 | 내용 |
|------|------|
| 정의 | 앞뒤가 같은 문자열 |
| 단순 확인 | `s == s[::-1]` |
| 고급 방식 | 투 포인터, 중심 확장, 마나커 알고리즘 |
| 시간 복잡도 | O(n) ~ O(n²) |
| 활용 | 문자열 처리, 유전체 분석, 패턴 인식 등 |

