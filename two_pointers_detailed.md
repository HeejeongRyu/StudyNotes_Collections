
# 📘 투 포인터(Two Pointers) 알고리즘 - 코딩 테스트 단골 알고리즘

---

## ✅ 1. 투 포인터란?

> **투 포인터(Two Pointers)** 알고리즘은 리스트나 배열에서 두 개의 포인터(인덱스)를 사용하여 탐색 범위를 조절하면서 문제를 해결하는 방법입니다.

- 정렬된 배열이나 리스트에 자주 사용됨
- 시간 복잡도를 O(n²) → O(n)으로 줄일 수 있음
- **연속된 구간**, **쌍의 합**, **슬라이딩 윈도우** 등에 효과적

---

## ✅ 2. 동작 방식

| 구성 요소 | 설명 |
|-----------|------|
| 시작 포인터 | 왼쪽 또는 배열의 시작점 |
| 끝 포인터 | 오른쪽 또는 탐색을 도와주는 인덱스 |
| 포인터 이동 | 조건에 따라 둘 중 하나 또는 양쪽을 이동 |

---

## ✅ 3. 대표 사용 예

1. **정렬된 배열에서 두 수의 합 찾기**
2. **연속 부분 배열의 합 또는 조건 만족 여부**
3. **슬라이딩 윈도우처럼 고정된 범위 탐색**

---

## ✅ 4. 예제 1: 두 수의 합 (Two Sum)

```python
def two_sum(arr, target):
    arr.sort()
    left, right = 0, len(arr) - 1

    while left < right:
        total = arr[left] + arr[right]
        if total == target:
            return (arr[left], arr[right])
        elif total < target:
            left += 1
        else:
            right -= 1
    return None
```

---

## ✅ 5. 예제 2: 연속 부분 수열의 합 (투 포인터)

```python
def count_subarrays_with_sum(arr, target):
    left, right = 0, 0
    total = 0
    count = 0
    n = len(arr)

    while right < n:
        total += arr[right]
        while total > target:
            total -= arr[left]
            left += 1
        if total == target:
            count += 1
        right += 1
    return count
```

---

## ✅ 6. 예제 3: 고정된 윈도우 최대값 (슬라이딩 윈도우)

```python
def max_in_sliding_window(arr, k):
    result = []
    for i in range(len(arr) - k + 1):
        result.append(max(arr[i:i+k]))
    return result
```

> 시간 최적화를 위해 `deque`를 사용하는 방식도 있음

---

## ✅ 7. 시간 복잡도

| 상황 | 복잡도 |
|------|--------|
| 배열 순회 | O(n) |
| 정렬 포함 | O(n log n) |
| 슬라이딩 윈도우 | O(n) (deque 사용 시) |

---

## ✅ 8. 주의사항

- 포인터가 겹치지 않도록 조건 (`left < right`, `left <= right`) 확인
- 배열이 정렬되어 있어야 할 수도 있음
- 무한 루프 방지 조건 설정 필요

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 투 포인터 (Two Pointers) |
| 포인터 개수 | 2개 |
| 활용 분야 | 부분합, 정렬, 쌍 탐색, 슬라이딩 윈도우 |
| 시간 복잡도 | O(n) 또는 O(n log n) |
| 핵심 전략 | 포인터를 조건에 따라 이동시키며 범위 축소/확대 |
