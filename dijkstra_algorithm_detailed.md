
# 📘 다익스트라 알고리즘 (Dijkstra's Algorithm) - 최단 경로 탐색 알고리즘

---

## ✅ 1. 다익스트라 알고리즘이란?

> 다익스트라(Dijkstra's) 알고리즘은 **가중치가 있는 그래프**에서 **시작 노드로부터 모든 노드까지의 최단 경로**를 찾는 알고리즘입니다.

- **음의 가중치가 없는 그래프**에서만 동작
- 대표적인 **최단 경로 알고리즘**
- 최단 거리 테이블을 갱신해가며 **한 번 방문한 노드는 다시 방문하지 않음**

---

## ✅ 2. 알고리즘의 원리

1. 시작 노드에서 출발하여 인접한 노드의 거리 값을 갱신
2. 아직 방문하지 않은 노드 중 **가장 거리가 짧은 노드** 선택
3. 그 노드를 거쳐 다른 노드로 가는 거리 계산 → 더 짧으면 갱신
4. 모든 노드를 방문할 때까지 반복

---

## ✅ 3. 적용 조건

| 조건 | 설명 |
|------|------|
| 그래프 종류 | 방향/무방향 모두 가능 |
| 가중치 | ✅ 음수 불가 |
| 시간 복잡도 | O(E log V) (힙 사용 시) |

---

## ✅ 4. Python 코드 구현 (우선순위 큐 활용)

```python
import heapq

def dijkstra(graph, start):
    distance = {node: float('inf') for node in graph}
    distance[start] = 0
    queue = [(0, start)]

    while queue:
        current_dist, current_node = heapq.heappop(queue)

        if current_dist > distance[current_node]:
            continue

        for neighbor, weight in graph[current_node]:
            new_dist = current_dist + weight
            if new_dist < distance[neighbor]:
                distance[neighbor] = new_dist
                heapq.heappush(queue, (new_dist, neighbor))

    return distance
```

---

## ✅ 5. 그래프 예시

```python
graph = {
    'A': [('B', 5), ('C', 1)],
    'B': [('A', 5), ('C', 2), ('D', 1)],
    'C': [('A', 1), ('B', 2), ('D', 4), ('E', 8)],
    'D': [('B', 1), ('C', 4), ('E', 3), ('F', 6)],
    'E': [('C', 8), ('D', 3)],
    'F': [('D', 6)]
}

print(dijkstra(graph, 'A'))
```

---

## ✅ 6. 시간 복잡도

| 연산 | 복잡도 |
|------|--------|
| 우선순위 큐 기반 | O((V + E) log V) |
| 단순 구현 (배열) | O(V²) |

---

## ✅ 7. 실전 사용 사례

- 지도에서 경로 탐색 (네이버, 카카오맵)
- 네트워크 라우팅
- 게임 캐릭터 경로 찾기
- 최소비용 계산

---

## ✅ 8. 변형 알고리즘

| 알고리즘 | 설명 |
|----------|------|
| 벨만-포드 | 음수 가중치 허용 (느림) |
| 플로이드-워셜 | 모든 노드 쌍 최단 경로 |
| A* 알고리즘 | 휴리스틱 기반 최적 경로 탐색 |

---

## 🎯 마무리 요약

| 항목 | 내용 |
|------|------|
| 알고리즘명 | 다익스트라 |
| 적용 조건 | 양의 가중치 그래프 |
| 복잡도 | O(E log V) |
| 핵심 자료구조 | 우선순위 큐 (heapq) |
| 활용 분야 | 지도, 게임, 네트워크, 물류 |

