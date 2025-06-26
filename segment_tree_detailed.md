
# 📘 세그먼트 트리 (Segment Tree)

---

## ✅ 1. 세그먼트 트리란?

> 세그먼트 트리는 주어진 구간에 대한 **합**, **최대/최소값**, **최빈값** 등을 **빠르게 계산**할 수 있도록 도와주는 트리 기반의 자료구조입니다.

- **범위 쿼리(Range Query)**와 **구간 업데이트**에 매우 효율적
- 배열의 특정 범위에 대한 값을 빠르게 구해야 할 때 사용
- 주로 **O(log n)** 시간 복잡도로 동작

---

## ✅ 2. 핵심 아이디어

- 완전 이진 트리 형태로 구성
- 각 노드는 특정 범위 `[l, r]`에 대한 정보를 저장
- 루트는 전체 구간을, 리프는 개별 원소를 나타냄

---

## ✅ 3. 사용 목적

| 목적 | 설명 |
|------|------|
| 구간 합 구하기 | 특정 범위의 합 |
| 구간 최대/최소값 | 범위 내 최댓값, 최솟값 |
| 구간 곱/최빈값 | 기타 연산 |

---

## ✅ 4. 기본 동작

1. **트리 구성(build)**: 재귀적으로 구간을 분할하여 노드 생성
2. **쿼리(query)**: 특정 범위의 값 계산
3. **갱신(update)**: 배열의 값 변경 → 관련 노드 갱신

---

## ✅ 5. 예제 (Python) - 구간 합 쿼리

```python
class SegmentTree:
    def __init__(self, data):
        self.n = len(data)
        self.tree = [0] * (4 * self.n)
        self.build(data, 1, 0, self.n - 1)

    def build(self, data, node, start, end):
        if start == end:
            self.tree[node] = data[start]
        else:
            mid = (start + end) // 2
            self.build(data, node * 2, start, mid)
            self.build(data, node * 2 + 1, mid + 1, end)
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    def query(self, l, r, node=1, start=0, end=None):
        if end is None:
            end = self.n - 1

        if r < start or end < l:
            return 0
        if l <= start and end <= r:
            return self.tree[node]

        mid = (start + end) // 2
        left_sum = self.query(l, r, node * 2, start, mid)
        right_sum = self.query(l, r, node * 2 + 1, mid + 1, end)
        return left_sum + right_sum

    def update(self, idx, value, node=1, start=0, end=None):
        if end is None:
            end = self.n - 1

        if start == end:
            self.tree[node] = value
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(idx, value, node * 2, start, mid)
            else:
                self.update(idx, value, node * 2 + 1, mid + 1, end)

            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

# 사용 예시
arr = [1, 3, 5, 7, 9, 11]
seg = SegmentTree(arr)
print(seg.query(1, 3))  # 출력: 15 (3 + 5 + 7)
seg.update(1, 10)
print(seg.query(1, 3))  # 출력: 22 (10 + 5 + 7)
```

---

## ✅ 6. 시간 및 공간 복잡도

| 연산 | 복잡도 |
|------|--------|
| 트리 구성 | O(n) |
| 쿼리(Query) | O(log n) |
| 업데이트(Update) | O(log n) |
| 공간 복잡도 | O(n) ~ O(4n) |

---

## ✅ 7. 세그먼트 트리 vs 다른 자료구조

| 구조 | 구간합 | 업데이트 | 공간 |
|------|--------|----------|------|
| 배열 | O(n) | O(1) | O(n) |
| 누적합 | O(1) | O(n) | O(n) |
| 세그먼트 트리 | O(log n) | O(log n) | O(n) ~ O(4n) |

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 세그먼트 트리 (Segment Tree) |
| 주요 기능 | 범위 쿼리, 구간 업데이트 |
| 시간 복잡도 | O(log n) |
| 공간 복잡도 | O(n) ~ O(4n) |
| 활용 | 구간합, 최대/최소값, 동적 배열 계산 |

