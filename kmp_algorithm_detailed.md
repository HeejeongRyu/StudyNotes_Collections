
# 📘 KMP 알고리즘 (Knuth-Morris-Pratt)

---

## ✅ 1. 개요

> KMP 알고리즘은 문자열에서 **부분 문자열(패턴)**을 빠르게 찾기 위한 탐색 알고리즘입니다.

- 문자열 검색 알고리즘 중 하나
- 접두사와 접미사의 일치 정보를 이용하여 불필요한 비교를 줄임
- 시간 복잡도: **O(N + M)** (텍스트 길이 N, 패턴 길이 M)

---

## ✅ 2. 핵심 아이디어

- **접두사 = 접미사** 관계를 이용하여 중복 비교를 줄임
- 실패함수(Failure Function, Pi 배열)를 사전 계산
- 탐색 중 불일치 발생 시, 패턴을 처음부터 다시 비교하지 않고 이동

---

## ✅ 3. 실패 함수(Pi 배열)

- `pi[i]`는 `pattern[0:i]`에서의 접두사이자 접미사인 문자열의 최대 길이
- 패턴의 내부 규칙을 기억해서 재탐색 줄임

예: 패턴 = "ababc"
- pi 배열 = [0, 0, 1, 2, 0]

---

## ✅ 4. 예제 코드 (Python)

```python
def get_pi(pattern):
    m = len(pattern)
    pi = [0] * m
    j = 0

    for i in range(1, m):
        while j > 0 and pattern[i] != pattern[j]:
            j = pi[j - 1]
        if pattern[i] == pattern[j]:
            j += 1
            pi[i] = j
    return pi

def kmp(text, pattern):
    n, m = len(text), len(pattern)
    pi = get_pi(pattern)
    result = []
    j = 0

    for i in range(n):
        while j > 0 and text[i] != pattern[j]:
            j = pi[j - 1]
        if text[i] == pattern[j]:
            if j == m - 1:
                result.append(i - m + 1)  # 매칭된 시작 인덱스
                j = pi[j]
            else:
                j += 1
    return result

# 사용 예시
text = "ababcababcabc"
pattern = "ababc"
matches = kmp(text, pattern)
print("패턴 위치:", matches)
```

---

## ✅ 5. 시간 및 공간 복잡도

| 항목 | 복잡도 |
|------|--------|
| 전처리(Pi 배열 계산) | O(M) |
| 탐색(Search) | O(N) |
| 전체 시간 복잡도 | O(N + M) |
| 공간 복잡도 | O(M) |

---

## ✅ 6. KMP vs 다른 알고리즘

| 알고리즘 | 시간 복잡도 | 특징 |
|----------|--------------|------|
| Brute Force | O(N*M) | 단순 비교 |
| KMP | O(N+M) | 접두/접미사 활용 |
| Rabin-Karp | O(N+M) 평균 | 해시 기반 |
| Boyer-Moore | O(N/M) 평균 | 뒤에서 비교 |

---

## ✅ 7. 활용 사례

| 분야 | 설명 |
|------|------|
| 텍스트 검색 | 문자열 검색 기능 구현 |
| DNA 분석 | 특정 유전자 패턴 탐색 |
| 보안 분석 | 악성코드 시그니처 탐지 |

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | KMP (Knuth-Morris-Pratt) |
| 주요 기능 | 문자열 검색 |
| 시간 복잡도 | O(N + M) |
| 핵심 개념 | 실패함수 기반 접두/접미사 분석 |
| 특징 | 효율적이고 빠름 |

