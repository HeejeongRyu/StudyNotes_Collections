
# 📘 병합 정렬 (Merge Sort) 상세 설명

---

## ✅ 1. 병합 정렬이란?

> **병합 정렬(Merge Sort)**은 배열을 **반으로 나눈 후 정렬**하고, **두 배열을 병합(merge)**하여 정렬을 완료하는 **분할 정복 기반**의 정렬 알고리즘입니다.

- 항상 **O(n log n)** 보장
- **안정 정렬**: 동일한 값의 순서 보존
- 추가 메모리 사용: **비교적 메모리 많이 사용**

---

## ✅ 2. 핵심 원리: **분할 정복(Divide and Conquer)**

### 📌 세 단계 구성

1. **분할(Divide)**  
   배열을 반으로 계속 나눔 → 최소 단위 1개가 될 때까지

2. **정복(Conquer)**  
   나뉜 배열들을 재귀적으로 정렬

3. **병합(Combine)**  
   두 개의 정렬된 배열을 하나로 **병합하면서 정렬 유지**

---

## ✅ 3. 작동 방식 (오름차순 기준)

1. 배열을 반으로 나눔
2. 각각 재귀적으로 병합 정렬 수행
3. 정렬된 두 배열을 **병합(merge)**

---

## ✅ 4. 예시로 이해하기

### 🔹 정렬 대상: `[5, 3, 8, 4, 2, 7, 1, 6]`

1. 분할 단계:

```
[5, 3, 8, 4]   |   [2, 7, 1, 6]
[5, 3] [8, 4]  |  [2, 7] [1, 6]
[5] [3] [8] [4]  [2] [7] [1] [6]
```

2. 병합 단계:

```
[3, 5] [4, 8] → [3, 4, 5, 8]
[2, 7] [1, 6] → [1, 2, 6, 7]
→ 최종 병합: [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## ✅ 5. 병합 정렬 시각화

```text
분할: [5, 3, 8, 4, 2, 7, 1, 6]
↓
→ [5, 3, 8, 4] + [2, 7, 1, 6]
→ [3, 5, 4, 8] + [2, 7, 1, 6]
→ [3, 4, 5, 8] + [1, 2, 6, 7]
→ [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## ✅ 6. 병합 정렬 코드 구현

### 🔹 JavaScript

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);
}

function merge(left, right) {
  const result = [];

  while (left.length && right.length) {
    if (left[0] <= right[0]) result.push(left.shift());
    else result.push(right.shift());
  }

  return result.concat(left, right);
}
```

### 🔹 Java

```java
void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = (left + right) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

void merge(int[] arr, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left, j = mid + 1, k = 0;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++];
        else temp[k++] = arr[j++];
    }

    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];

    for (int t = 0; t < temp.length; t++) {
        arr[left + t] = temp[t];
    }
}
```

---

## ✅ 7. 시간 복잡도

| 경우 | 시간 복잡도 | 설명 |
|------|--------------|------|
| 최선 | O(n log n) | 항상 동일 |
| 평균 | O(n log n) | 항상 동일 |
| 최악 | O(n log n) | 항상 동일 |

---

## ✅ 8. 공간 복잡도

- **O(n)** (병합할 때 추가 배열 필요)
- In-place로 구현 불가 (추가 메모리 사용)

---

## ✅ 9. 병합 정렬의 특징 정리

| 항목 | 설명 |
|------|------|
| 정렬 방식 | 분할 정복 (merge 중심) |
| 안정성 | ✅ 안정 정렬 |
| 메모리 사용 | 높음 (O(n)) |
| 시간 복잡도 | 항상 O(n log n) |
| 구현 난이도 | 중간 (merge 구현 필요)

---

## ✅ 10. 장점과 단점

### 🔹 장점

- **시간 복잡도 보장**: 항상 O(n log n)
- **안정 정렬**: 중복 값의 순서 유지
- **링크드 리스트** 정렬에 매우 유리 (포인터 이동으로 merge 가능)

### 🔹 단점

- 추가 메모리 사용 (배열 기반일 경우)
- In-place 정렬 아님 → 대용량 배열에서 비효율적

---

## ✅ 11. 병합 정렬이 적합한 상황

| 상황 | 설명 |
|------|------|
| 정렬 안정성이 중요한 경우 | 중복 값의 순서가 중요할 때 |
| 큰 데이터를 정렬할 때 | 외부 정렬에 유리 (디스크, 파일 기반 등) |
| 연결 리스트 정렬 | 포인터만 조작하면 되므로 효율적

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 정렬 방식 | 분할 정복, 병합 중심 |
| 시간 복잡도 | 항상 O(n log n) |
| 공간 복잡도 | O(n) |
| 정렬 안정성 | ✅ 안정 정렬 |
| 사용 적합 | 연결 리스트, 정렬 안정성 필요, 대용량 외부 정렬 등 |
