
# 📘 유니온 파인드(Disjoint Set Union, Union-Find) 알고리즘

---

## ✅ 1. 유니온 파인드란?

> 유니온 파인드(또는 분리 집합, Disjoint Set)는 원소들이 어떤 집합에 속해 있는지 빠르게 확인하고, 두 집합을 하나로 합치는 연산을 빠르게 수행할 수 있도록 도와주는 자료구조입니다.

- 그래프의 **사이클 판별**, **네트워크 연결성 판단** 등에서 활용
- 대표적인 **상호 배타적 집합(Mutually Exclusive Set)** 알고리즘

---

## ✅ 2. 핵심 연산

| 연산 | 설명 |
|------|------|
| `find(x)` | 원소 x가 속한 **대표(부모)** 노드를 찾음 |
| `union(x, y)` | 원소 x와 y가 속한 **집합을 합침** |

---

## ✅ 3. 동작 원리

1. 각각의 노드는 자기 자신을 부모로 가짐
2. `find` 연산은 **루트 노드**를 찾아 반환
3. `union` 연산은 두 루트 노드를 연결 (작은 트리 → 큰 트리에 붙이기)
4. 최적화를 위해 **경로 압축(Path Compression)**과 **랭크 정렬(Union by Rank)** 사용

---

## ✅ 4. 기본 구현 예시 (Python)

```python
# 초기화
parent = [i for i in range(10)]

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])  # 경로 압축
    return parent[x]

def union(x, y):
    root_x = find(x)
    root_y = find(y)

    if root_x != root_y:
        parent[root_y] = root_x  # y 루트를 x 루트에 붙임
```

---

## ✅ 5. 사용 예시: 사이클 판별

```python
edges = [(1, 2), (2, 3), (3, 4), (4, 1)]  # 사이클 있음

parent = [i for i in range(5)]

def has_cycle(edges):
    for a, b in edges:
        if find(a) == find(b):
            return True  # 사이클 발생
        union(a, b)
    return False

print(has_cycle(edges))  # 출력: True
```

---

## ✅ 6. 시간 복잡도

- **`find`/`union` 연산 평균 시간 복잡도**: O(α(N))  
  (여기서 α는 아커만 함수의 역함수로 거의 상수에 가까움)

---

## ✅ 7. 활용 문제

| 문제 유형 | 예시 |
|-----------|------|
| 사이클 탐지 | 그래프 내 루프 확인 |
| 네트워크 연결 여부 | 같은 집합인지 확인 |
| 크루스칼 MST 알고리즘 | 최소 신장 트리에서 간선 선택 시 |

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 유니온 파인드 (Disjoint Set, Union-Find) |
| 핵심 연산 | `find`, `union` |
| 시간 복잡도 | O(α(N)) |
| 대표 활용 | 사이클 탐지, 집합 병합, MST |

