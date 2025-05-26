
# 📘 힙 정렬 (Heap Sort) 상세 설명

---

## ✅ 1. 힙 정렬이란?

> **힙 정렬(Heap Sort)**은 **힙(Heap)**이라는 **완전 이진 트리 기반의 자료구조**를 사용하여 가장 큰(또는 작은) 값을 빠르게 찾아 정렬하는 알고리즘입니다.

- 평균/최악 성능: **O(n log n)**
- **불안정 정렬**
- **In-place 정렬** 가능

---

## ✅ 2. 정렬 과정 요약

1. 배열을 **Max Heap 구조로 변환**
2. **루트(가장 큰 값)**를 맨 뒤로 보내고, 힙 크기를 줄임
3. 다시 Heapify하여 최대값을 찾아 뒤로 보냄
4. 반복 → 정렬 완료

---

## ✅ 3. Heap 구조와 특징

| 특징 | 설명 |
|------|------|
| 구조 | 완전 이진 트리 (배열로 표현 가능) |
| 루트 노드 | 항상 최댓값 (Max Heap 기준) |
| 삽입/삭제 | O(log n) |
| 부모 인덱스 | `i` → 부모는 `(i - 1) / 2` |
| 자식 인덱스 | `i` → 왼쪽 `2i + 1`, 오른쪽 `2i + 2` |

---

## ✅ 4. 예시로 이해하기

정렬 대상: `[4, 10, 3, 5, 1]`

1. Max Heap 변환 → `[10, 5, 3, 4, 1]`
2. 10 ↔ 1 → `[1, 5, 3, 4, 10]`
3. Heapify → `[5, 4, 3, 1, 10]`
4. 반복 → 최종 결과 `[1, 3, 4, 5, 10]`

---

## ✅ 5. 힙 정렬 시각화

```
초기:     [4, 10, 3, 5, 1]
Heapify:  [10, 5, 3, 4, 1]
정렬과정: [1, 5, 3, 4, 10] → [5, 4, 3, 1, 10] → ...
최종:     [1, 3, 4, 5, 10]
```

---

## ✅ 6. 힙 정렬 코드 구현

### JavaScript

```javascript
function heapSort(arr) {
  const n = arr.length;

  for (let i = Math.floor(n / 2) - 1; i >= 0; i--) {
    heapify(arr, n, i);
  }

  for (let i = n - 1; i > 0; i--) {
    [arr[0], arr[i]] = [arr[i], arr[0]];
    heapify(arr, i, 0);
  }

  return arr;
}

function heapify(arr, n, i) {
  let largest = i;
  const left = 2 * i + 1;
  const right = 2 * i + 2;

  if (left < n && arr[left] > arr[largest]) largest = left;
  if (right < n && arr[right] > arr[largest]) largest = right;

  if (largest !== i) {
    [arr[i], arr[largest]] = [arr[largest], arr[i]];
    heapify(arr, n, largest);
  }
}
```

### Java

```java
void heapSort(int[] arr) {
    int n = arr.length;

    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);

    for (int i = n - 1; i > 0; i--) {
        int temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;

        heapify(arr, i, 0);
    }
}

void heapify(int[] arr, int n, int i) {
    int largest = i;
    int l = 2 * i + 1;
    int r = 2 * i + 2;

    if (l < n && arr[l] > arr[largest])
        largest = l;

    if (r < n && arr[r] > arr[largest])
        largest = r;

    if (largest != i) {
        int swap = arr[i];
        arr[i] = arr[largest];
        arr[largest] = swap;

        heapify(arr, n, largest);
    }
}
```

---

## ✅ 7. 시간 복잡도

| 단계 | 복잡도 |
|------|--------|
| Build Heap | O(n) |
| 정렬 (n * log n) | O(n log n) |
| 총합 | **O(n log n)** |

---

## ✅ 8. 공간 복잡도

- **O(1)**  
(In-place 정렬, 추가 메모리 사용 거의 없음)

---

## ✅ 9. 힙 정렬의 특징 정리

| 항목 | 설명 |
|------|------|
| 정렬 방식 | Heap 자료구조 기반 |
| 안정성 | ❌ 불안정 정렬 |
| 메모리 | ✅ In-place |
| 시간 복잡도 | 항상 O(n log n) |
| 구현 난이도 | 중간 이상 (heapify 구현 필요)

---

## ✅ 10. 장점과 단점

### 장점

- 항상 일정한 시간 성능 O(n log n)
- 추가 메모리 필요 없음 (In-place)
- 대용량에서도 안정적인 성능

### 단점

- **불안정 정렬**
- **퀵 정렬보다 평균 성능 낮음**
- 구현 복잡도 상대적으로 있음

---

## ✅ 11. 힙 정렬이 적합한 상황

| 상황 | 설명 |
|------|------|
| 메모리 제약 환경 | In-place 정렬 필요 시 |
| 최악 시간 복잡도 보장 | 항상 O(n log n) 보장됨 |
| 정렬 안정성 필요 없는 경우 | 동일 값 순서 중요하지 않을 때

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 정렬 기반 | Heap 자료구조 |
| 시간 복잡도 | O(n log n) |
| 공간 복잡도 | O(1) |
| 안정성 | ❌ 불안정 |
| 특성 | 빠르면서도 메모리 효율적인 정렬 |
