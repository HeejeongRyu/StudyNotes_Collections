
# 📘 너비 우선 탐색(BFS: Breadth-First Search) 알고리즘

---

## ✅ 1. BFS란?

> **너비 우선 탐색(Breadth-First Search, BFS)**은 그래프나 트리에서 시작 노드로부터 가까운 노드부터 차례대로 탐색해 나가는 알고리즘입니다.

- **큐(Queue)** 자료구조를 이용
- 모든 노드를 최단 거리 순서로 탐색
- 그래프 이론, 경로 탐색, 미로 문제 등에서 자주 등장

---

## ✅ 2. 동작 원리

1. 시작 노드를 큐에 넣고 방문 처리
2. 큐에서 노드를 꺼냄
3. 해당 노드의 **인접 노드들 중 아직 방문하지 않은 노드**를 모두 큐에 넣고 방문 처리
4. 큐가 빌 때까지 반복

---

## ✅ 3. 시각적 흐름 예시

```
그래프:
A - B - C
|   |
D - E

BFS 순서 (시작: A): A → B → D → C → E
```

---

## ✅ 4. 기본 구현 (Python)

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)

    while queue:
        node = queue.popleft()
        print(node, end=' ')

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### 사용 예시:

```python
graph = {
    'A': ['B', 'D'],
    'B': ['A', 'C', 'E'],
    'C': ['B'],
    'D': ['A', 'E'],
    'E': ['B', 'D']
}

bfs(graph, 'A')  # 출력: A B D C E
```

---

## ✅ 5. BFS와 최단 거리 (가중치가 없는 그래프)

- BFS는 **최단 경로** 탐색에도 자주 사용됩니다.
- 각 노드를 처음 방문하는 순간이 곧 최단 거리임이 보장됨.

```python
def bfs_shortest_path(graph, start):
    visited = {start}
    queue = deque([(start, 0)])  # (node, distance)

    while queue:
        node, dist = queue.popleft()
        print(f"{node}까지의 거리: {dist}")

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
```

---

## ✅ 6. BFS의 시간 및 공간 복잡도

- **시간 복잡도**: O(V + E)  
  (V: 정점 수, E: 간선 수)
- **공간 복잡도**: O(V) (방문 배열, 큐 등)

---

## ✅ 7. BFS vs DFS 비교

| 항목 | BFS | DFS |
|------|-----|-----|
| 자료구조 | Queue | Stack (재귀) |
| 순서 | 가까운 노드부터 | 깊은 노드부터 |
| 용도 | 최단 거리 탐색 | 경로 존재 여부, 백트래킹 |
| 구현 | 반복적 | 재귀 또는 반복 |

---

## ✅ 8. 주의사항

- 그래프는 **인접 리스트**로 표현하는 것이 일반적
- 방문 여부를 확인하지 않으면 무한 루프 발생 가능
- 2차원 배열/미로 문제에서는 `dx`, `dy` 배열과 함께 BFS를 응용함

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | BFS (Breadth-First Search) |
| 탐색 순서 | 가까운 노드부터 |
| 자료구조 | Queue |
| 시간 복잡도 | O(V + E) |
| 대표 문제 | 최단 경로 탐색, 미로, 그래프 탐색 |

