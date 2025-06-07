
# 📘 유니온 파인드 (Union-Find) - 분리 집합 알고리즘

---

## ✅ 1. 유니온 파인드란?

> 유니온 파인드(Union-Find) 알고리즘은 **서로소 집합(Disjoint Set)** 자료구조를 구현하는 알고리즘입니다.

- 여러 개의 노드가 **서로 다른 집합에 속해 있는지 판단**하거나,
- 두 노드를 **같은 집합으로 합치는 연산**을 수행합니다.

---

## ✅ 2. 주요 연산

| 연산 | 설명 |
|------|------|
| Find | 특정 원소가 속한 집합 찾기 (루트 노드) |
| Union | 두 원소가 속한 집합을 하나로 합치기 |

---

## ✅ 3. 대표 사용 사례

- **그래프 사이클 판별**
- **크루스칼 알고리즘** (최소 신장 트리)
- **네트워크 연결 여부 판단**

---

## ✅ 4. Python 기본 구현

```python
# 초기화
def make_set(n):
    return [i for i in range(n)]

# Find: 경로 압축 적용
def find(parent, x):
    if parent[x] != x:
        parent[x] = find(parent, parent[x])
    return parent[x]

# Union
def union(parent, a, b):
    root_a = find(parent, a)
    root_b = find(parent, b)
    if root_a != root_b:
        parent[root_b] = root_a
```

---

## ✅ 5. 예제

```python
parent = make_set(7)
union(parent, 1, 2)
union(parent, 2, 3)
union(parent, 4, 5)

print(find(parent, 1))  # 1
print(find(parent, 3))  # 1
print(find(parent, 4))  # 4
print(find(parent, 5))  # 4
print(find(parent, 6))  # 6
```

---

## ✅ 6. 시간 복잡도

| 연산 | 복잡도 |
|------|--------|
| Find (경로 압축 사용) | 거의 O(1) |
| Union (크기/랭크 고려) | 거의 O(1) |

- 실제 시간 복잡도는 **O(α(n))**으로 거의 상수에 가까움 (α는 아커만 함수의 역함수)

---

## ✅ 7. 경로 압축 & 랭크 최적화

### 경로 압축(Path Compression)
- Find 시 루트 노드를 부모로 직접 설정 → 트리 높이 줄어듦

### 랭크 기반 유니온(Rank Union)
- 노드 수(또는 트리 깊이)가 더 낮은 쪽을 큰 쪽에 붙임

---

## ✅ 8. 그래프 사이클 판별 예시

```python
edges = [(1, 2), (1, 3), (2, 3)]  # 사이클 존재
parent = make_set(4)

cycle = False
for a, b in edges:
    if find(parent, a) == find(parent, b):
        cycle = True
        break
    union(parent, a, b)

print("사이클 존재" if cycle else "사이클 없음")
```

---

## 🎯 마무리 요약

| 항목 | 내용 |
|------|------|
| 알고리즘명 | 유니온 파인드 (Disjoint Set) |
| 주요 연산 | Union, Find |
| 최적화 | 경로 압축 + 랭크 |
| 활용 분야 | 사이클 탐지, MST(크루스칼), 그룹 판별 |
| 시간 복잡도 | O(α(n)) (거의 O(1)) |

