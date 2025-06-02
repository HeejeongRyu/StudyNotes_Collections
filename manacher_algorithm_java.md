
# 📘 마나커 알고리즘 (Manacher's Algorithm) - Java 기반 상세 설명

---

## ✅ 1. 마나커 알고리즘이란?

> 마나커 알고리즘(Manacher's Algorithm)은 문자열에서 **최장 팰린드롬 부분 문자열**을 **O(n)** 시간 복잡도로 찾는 고급 알고리즘입니다.

- 일반적인 중심 확장 방식은 **O(n²)**이지만,
- 마나커 알고리즘은 **이전 계산 결과를 활용하여 중복 계산을 줄임**으로써 **O(n)**으로 최적화

---

## ✅ 2. 핵심 아이디어

1. 문자열을 전처리하여 홀/짝 구분 없이 다루기 위해 `#` 문자를 삽입
   - 예: `"abba"` → `"#a#b#b#a#"`
2. 각 문자를 중심으로 확장 가능한 팰린드롬 반경을 계산해 배열 `p[]`에 저장
3. 현재 팰린드롬의 **가장 오른쪽 끝**을 `right`, 중심을 `center`로 관리
4. 대칭되는 위치의 `p[i_mirror]` 값을 이용해 **확장 가능성**을 예측하고 불필요한 계산을 줄임

---

## ✅ 3. Java 코드 구현

```java
public class Manacher {
    public static String longestPalindrome(String s) {
        if (s == null || s.length() == 0) return "";

        // 1. 전처리
        StringBuilder sb = new StringBuilder();
        sb.append("^"); // 시작 마커
        for (char c : s.toCharArray()) {
            sb.append("#").append(c);
        }
        sb.append("#$"); // 끝 마커
        char[] arr = sb.toString().toCharArray();

        int[] p = new int[arr.length]; // 팰린드롬 반경 배열
        int center = 0, right = 0;

        // 2. 반경 계산
        for (int i = 1; i < arr.length - 1; i++) {
            int mirror = 2 * center - i;

            if (i < right)
                p[i] = Math.min(right - i, p[mirror]);

            // 중심 확장
            while (arr[i + (1 + p[i])] == arr[i - (1 + p[i])]) {
                p[i]++;
            }

            // 중심과 오른쪽 갱신
            if (i + p[i] > right) {
                center = i;
                right = i + p[i];
            }
        }

        // 3. 가장 긴 팰린드롬 구간 찾기
        int maxLen = 0;
        int centerIndex = 0;
        for (int i = 1; i < p.length - 1; i++) {
            if (p[i] > maxLen) {
                maxLen = p[i];
                centerIndex = i;
            }
        }

        int start = (centerIndex - maxLen) / 2;
        return s.substring(start, start + maxLen);
    }

    public static void main(String[] args) {
        String input = "babad";
        System.out.println("최장 팰린드롬: " + longestPalindrome(input));
    }
}
```

---

## ✅ 4. 시간 및 공간 복잡도

| 항목 | 복잡도 |
|------|--------|
| 시간 복잡도 | O(n) |
| 공간 복잡도 | O(n) (문자열 변환 및 반경 배열 사용) |

---

## ✅ 5. 마나커 알고리즘 장점

- 기존 O(n²)의 중심 확장 알고리즘보다 **훨씬 빠름**
- 긴 문자열에서 **최장 팰린드롬 탐색 시 매우 유리**
- 정규화 문자열 처리로 **짝수/홀수** 구분 불필요

---

## ✅ 6. 전처리 구조 요약

```text
입력: abba
전처리: ^#a#b#b#a#$
p[] 결과 (예시): [0, 0, 1, 0, 3, 0, 1, 0, 0]

중심점: 4 → 반경: 3 → 길이: 3 → 원래 문자열에서 위치 계산 필요
```

---

## 🎯 마무리 요약

| 항목 | 내용 |
|------|------|
| 정의 | 최장 팰린드롬 부분 문자열 탐색용 알고리즘 |
| 시간 복잡도 | O(n) |
| 방식 | 중심 확장 + 메모이제이션 |
| 전처리 | `^#a#b#...#$` 형식으로 처리 |
| 구현 난이도 | 중상 (문자열 인덱스 처리 주의 필요) |

