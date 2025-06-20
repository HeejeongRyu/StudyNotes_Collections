
# 📘 슬라이딩 윈도우(Sliding Window) 알고리즘

---

## ✅ 1. 슬라이딩 윈도우란?

> **슬라이딩 윈도우(Sliding Window)**는 배열이나 리스트에서 일정한 범위를 유지하며 값을 비교하거나 계산할 때 사용하는 알고리즘 기법입니다.

- 고정 또는 가변 길이의 윈도우(범위)를 앞에서 뒤로 "슬라이드" 시키며 처리
- 주로 **최대/최소 값**, **부분합**, **개수 세기** 등의 문제에서 사용
- 시간 복잡도를 **O(n)** 으로 줄이는 데 유용

---

## ✅ 2. 동작 원리

1. 시작 인덱스(`start`)와 끝 인덱스(`end`)를 정의
2. 윈도우 내에서 원하는 조건을 만족하는 계산 수행
3. 조건에 따라 윈도우를 오른쪽으로 이동
4. 조건이 유지되는 동안 결과 갱신

---

## ✅ 3. 주요 사용 사례

| 문제 유형 | 예시 |
|-----------|------|
| 부분합 | 부분합이 K 이상인 최소 길이 |
| 최대/최소값 | 윈도우 내 최대값 구하기 |
| 고유 값 개수 | K 길이 윈도우 내 서로 다른 수 개수 |

---

## ✅ 4. 예제 1: 고정 길이 윈도우 내 최대 합

```python
def max_subarray_sum(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum

    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)

    return max_sum

print(max_subarray_sum([1, 2, 3, 4, 5, 6, 7, 8], 3))  # 출력: 21 (6+7+8)
```

---

## ✅ 5. 예제 2: 부분합이 S 이상인 최소 길이 (가변 윈도우)

```python
def min_subarray_len(target, nums):
    n = len(nums)
    left = 0
    total = 0
    min_len = float('inf')

    for right in range(n):
        total += nums[right]

        while total >= target:
            min_len = min(min_len, right - left + 1)
            total -= nums[left]
            left += 1

    return min_len if min_len != float('inf') else 0

print(min_subarray_len(15, [1,2,3,4,5,6,7,8,9]))  # 출력: 2 (8+9)
```

---

## ✅ 6. 시간 및 공간 복잡도

- **시간 복잡도**: O(n)
- **공간 복잡도**: O(1) 또는 O(k) (윈도우 길이)

---

## ✅ 7. 슬라이딩 윈도우 vs 브루트포스

| 항목 | 브루트포스 | 슬라이딩 윈도우 |
|------|------------|------------------|
| 복잡도 | O(nk) | O(n) |
| 구현 난이도 | 쉬움 | 약간의 구현 필요 |
| 효율성 | 낮음 | 매우 효율적 |

---

## ✅ 8. 응용 문제

- LeetCode 209. Minimum Size Subarray Sum
- Programmers: 보석 쇼핑, 연속된 부분 수열의 합
- 백준 12891 DNA 비밀번호

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 슬라이딩 윈도우 (Sliding Window) |
| 주 사용 목적 | 고정/가변 길이 범위 내 최적값 탐색 |
| 시간 복잡도 | O(n) |
| 대표 문제 | 부분합, 최대 합, 서로 다른 수 개수 |

