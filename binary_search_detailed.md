
# 📘 이진 탐색 (Binary Search) - 코딩 테스트 필수 알고리즘

---

## ✅ 1. 이진 탐색이란?

> 이진 탐색(Binary Search)은 **정렬된 배열**에서 특정 값을 빠르게 찾는 탐색 알고리즘입니다.

- 시간 복잡도는 **O(log n)** 으로 매우 빠름
- **선형 탐색**(O(n))보다 효율적
- 배열이 반드시 **오름차순 정렬**되어 있어야 함

---

## ✅ 2. 작동 방식 (예시)

### 예시 배열:
```
arr = [1, 3, 5, 7, 9, 11, 13, 15]
target = 9
```

### 과정 요약:
1. `left = 0`, `right = 7`
2. `mid = (0 + 7) / 2 = 3 → arr[3] = 7` → 9보다 작음 → 왼쪽 버리고 오른쪽 탐색
3. `left = 4`, `right = 7`, `mid = 5 → arr[5] = 11` → 9보다 큼 → 오른쪽 버리고 왼쪽 탐색
4. `left = 4`, `right = 4`, `mid = 4 → arr[4] = 9` → **찾음!**

---

## ✅ 3. Python 코드 구현

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1

    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

---

## ✅ 4. 재귀 방식 구현

```python
def binary_search_recursive(arr, target, left, right):
    if left > right:
        return -1
    mid = (left + right) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid + 1, right)
    else:
        return binary_search_recursive(arr, target, left, mid - 1)
```

---

## ✅ 5. 시간 복잡도 분석

| 항목 | 복잡도 |
|------|--------|
| 최선 | O(1) (한 번에 찾는 경우) |
| 평균 | O(log n) |
| 최악 | O(log n) |

---

## ✅ 6. 이진 탐색의 활용 예시

- **탐색 문제** (특정 값이 있는지 확인)
- **최솟값/최댓값 범위 조절 문제**
- **매개 변수 탐색 (parametric search)**  
  → ex) 특정 조건을 만족하는 최소/최대 값 구할 때

---

## ✅ 7. 주의할 점

| 조건 | 설명 |
|------|------|
| 배열 정렬 | 반드시 정렬된 상태여야 함 |
| 오버플로 방지 | `mid = left + (right - left) // 2` 권장 |
| 정수형만 탐색 가능? | 아니요. 문자열도 정렬돼 있다면 사용 가능 |

---

## ✅ 8. 실전 예제

> 문제: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 에서 7이 몇 번째 인덱스에 있는가?

```python
arr = [1,2,3,4,5,6,7,8,9,10]
print(binary_search(arr, 7))  # 결과: 6
```

---

## ✅ 9. Python 내장 모듈 `bisect`

```python
import bisect

arr = [1, 2, 4, 4, 4, 5, 6]

print(bisect.bisect_left(arr, 4))  # 가장 왼쪽 4의 위치 (2)
print(bisect.bisect_right(arr, 4)) # 가장 오른쪽 4 이후 위치 (5)
```

---

## 🎯 마무리 요약

| 항목 | 내용 |
|------|------|
| 알고리즘명 | 이진 탐색 (Binary Search) |
| 정렬 필요 | ✅ 반드시 정렬된 배열 |
| 시간 복잡도 | O(log n) |
| 활용 분야 | 정답의 범위를 빠르게 좁혀야 하는 모든 문제 |
| 추천 상황 | 이분 탐색, 매개변수 탐색, 최적화 문제 등 |

