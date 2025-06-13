
# 📘 그리디(Greedy) 알고리즘 - 탐욕적 선택으로 최적해 접근

---

## ✅ 1. 그리디 알고리즘이란?

> **그리디(Greedy)** 알고리즘은 매 단계에서 가장 좋아 보이는(최적의) 선택을 하는 방식으로 전체 최적해에 도달하고자 하는 알고리즘입니다.

- **매 순간 최선의 선택을 함**
- 부분적으로 최선의 선택이 전체적으로도 최선이라는 **탐욕 선택 속성(Greedy Choice Property)** 과 **최적 부분 구조(Optimal Substructure)** 를 만족해야 적용 가능

---

## ✅ 2. 핵심 특징

| 특징 | 설명 |
|------|------|
| 국지적 최적 | 현재 단계에서 가장 최선인 해 선택 |
| 전역적 최적 | 전체 문제의 최적해로 이어짐 |
| 되돌아가지 않음 | 한번 선택한 것은 되돌리지 않음 |
| 단순하고 빠름 | 구현이 간단하고 속도가 빠름 (O(n log n), O(n)) |

---

## ✅ 3. 그리디 알고리즘이 적용 가능한 조건

1. **Greedy Choice Property**: 현재의 최적 선택이 이후에도 최적 결과를 보장
2. **Optimal Substructure**: 문제의 전체 최적해가 부분 문제의 최적해로 구성됨

---

## ✅ 4. 예제 1: 동전 거스름돈

> 가장 큰 동전부터 차례로 사용

```python
def greedy_coin_change(coins, amount):
    coins.sort(reverse=True)
    count = 0

    for coin in coins:
        count += amount // coin
        amount %= coin

    return count if amount == 0 else -1

print(greedy_coin_change([500, 100, 50, 10], 1260))  # 출력: 6
```

> 하지만 그리디는 **항상 정답을 보장하지는 않음**  
> 예: 동전 단위가 [1, 3, 4]일 때 6을 만드는 최소 개수는 DP가 필요

---

## ✅ 5. 예제 2: 활동 선택 문제 (회의실 배정)

```python
def activity_selection(start, end):
    activities = sorted(zip(start, end), key=lambda x: x[1])  # 종료 시간 기준 정렬
    last_end = 0
    count = 0

    for s, e in activities:
        if s >= last_end:
            count += 1
            last_end = e

    return count

print(activity_selection([1, 3, 0, 5, 8, 5], [2, 4, 6, 7, 9, 9]))  # 출력: 4
```

---

## ✅ 6. 예제 3: 최소 신장 트리 (MST) - 크루스칼 알고리즘

```python
def kruskal(n, edges):
    parent = list(range(n))

    def find(u):
        if parent[u] != u:
            parent[u] = find(parent[u])
        return parent[u]

    def union(u, v):
        u_root = find(u)
        v_root = find(v)
        if u_root == v_root:
            return False
        parent[v_root] = u_root
        return True

    edges.sort(key=lambda x: x[2])  # 가중치 기준 정렬
    mst_weight = 0

    for u, v, w in edges:
        if union(u, v):
            mst_weight += w

    return mst_weight
```

---

## ✅ 7. 시간 복잡도

| 알고리즘 | 복잡도 |
|----------|--------|
| 동전 거스름돈 | O(n log n) |
| 활동 선택 | O(n log n) |
| 크루스칼 | O(E log E) |

---

## ✅ 8. 주의사항

- 그리디는 **정답을 보장하지 않는 경우도 있음**
- 반드시 탐욕 선택 속성과 최적 부분 구조가 만족되어야 함
- 보장되지 않는 문제는 **동적 계획법(DP)** 고려

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 그리디 알고리즘 (Greedy) |
| 핵심 전략 | 순간마다 최적 선택 |
| 되돌림 가능성 | 없음 |
| 적용 조건 | 탐욕 선택 속성 + 최적 부분 구조 |
| 대표 문제 | 동전 거스름돈, 회의실 배정, MST(크루스칼) |

