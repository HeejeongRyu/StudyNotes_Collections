
# 📘 깊이 우선 탐색(DFS: Depth-First Search) 알고리즘

---

## ✅ 1. DFS란?

> **깊이 우선 탐색(DFS, Depth-First Search)**은 그래프나 트리에서 한 방향으로 가능한 멀리까지 탐색한 후, 더 이상 갈 곳이 없으면 이전 분기로 돌아가 다른 방향을 탐색하는 방식입니다.

- **스택(Stack)** 또는 **재귀 호출**을 사용
- 미로 탐색, 경로 탐색, 백트래킹 문제에서 자주 사용
- DFS는 방문 순서보다는 구조 탐색에 유리

---

## ✅ 2. 동작 원리

1. 시작 노드에서 탐색 시작
2. 현재 노드를 **방문 처리**
3. 인접한 노드 중 방문하지 않은 노드를 **재귀적으로 방문**
4. 더 이상 방문할 노드가 없을 때 이전 노드로 되돌아감 (백트래킹)

---

## ✅ 3. DFS 흐름 예시

```
그래프:
A - B - C
|   |
D - E

DFS 순서 (시작: A): A → B → C → E → D
```

---

## ✅ 4. 기본 구현 (Python)

### 재귀 방식

```python
def dfs(graph, node, visited):
    visited.add(node)
    print(node, end=' ')

    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

### 호출 예시

```python
graph = {
    'A': ['B', 'D'],
    'B': ['C', 'E'],
    'C': [],
    'D': [],
    'E': []
}

visited = set()
dfs(graph, 'A', visited)
# 출력: A B C E D
```

---

## ✅ 5. 스택을 이용한 반복적 구현

```python
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]

    while stack:
        node = stack.pop()
        if node not in visited:
            print(node, end=' ')
            visited.add(node)
            stack.extend(reversed(graph[node]))
```

---

## ✅ 6. DFS의 활용 예시

- **미로 탐색**
- **백트래킹** (조합/순열)
- **사이클 탐지**
- **위상 정렬**
- **연결 요소 개수 찾기**

---

## ✅ 7. DFS vs BFS 비교

| 항목 | DFS | BFS |
|------|-----|-----|
| 탐색 방식 | 깊이 우선 | 너비 우선 |
| 자료구조 | Stack / Recursion | Queue |
| 구현 방식 | 재귀적 / 반복적 | 반복적 |
| 용도 | 백트래킹, 경로 탐색 | 최단 거리, 레벨 탐색 |

---

## ✅ 8. 시간 및 공간 복잡도

- **시간 복잡도**: O(V + E)
- **공간 복잡도**: O(V) (방문 집합, 스택 공간 등)

---

## ✅ 9. 주의사항

- 순환(사이클)이 있는 그래프에서는 반드시 **방문 체크**를 해야 무한 루프 방지
- 트리 구조에서는 DFS가 기본 탐색 방식
- 스택 오버플로우를 피하려면 **반복 구현**이 유리

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | DFS (Depth-First Search) |
| 탐색 순서 | 가능한 깊이까지 먼저 |
| 자료구조 | Stack / 재귀 |
| 시간 복잡도 | O(V + E) |
| 대표 문제 | 미로 탐색, 백트래킹, 경로 탐색 |

