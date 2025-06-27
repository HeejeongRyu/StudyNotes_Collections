
# 📘 플로이드-워셜 알고리즘 (Floyd-Warshall Algorithm)

---

## ✅ 1. 개요

> 플로이드-워셜(Floyd-Warshall) 알고리즘은 **모든 정점 간 최단 경로**를 구하는 알고리즘으로, **음의 가중치가 있는 간선**도 처리할 수 있는 장점이 있습니다.

- **모든 쌍 (All Pairs Shortest Paths)** 문제 해결
- 음수 가중치 허용, 단 **음의 사이클은 없어야 함**
- 동적 계획법 기반

---

## ✅ 2. 핵심 개념

- 거리 행렬 `dist[i][j]`는 `i → j`까지의 최단 거리
- 각 정점을 중간 경유지로 두고 `i → k → j`를 시도
- 점화식:  
  `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`

---

## ✅ 3. 동작 방식

1. 초기 거리 행렬 설정 (`dist[i][j] = weight`, 자기 자신은 0)
2. 중간 노드 `k`를 1개씩 증가시키며 모든 쌍을 갱신
3. 3중 for문 사용 → 시간 복잡도 O(N³)

---

## ✅ 4. 예제 (Python)

```python
INF = float('inf')

def floyd_warshall(n, edges):
    dist = [[INF] * n for _ in range(n)]

    # 초기값 세팅
    for i in range(n):
        dist[i][i] = 0

    for u, v, w in edges:
        dist[u][v] = w  # 방향 그래프

    # 알고리즘 수행
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][j] > dist[i][k] + dist[k][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]

    return dist

# 사용 예시
edges = [
    (0, 1, 4),
    (0, 2, 2),
    (1, 2, 5),
    (2, 1, 1),
    (1, 3, 1),
    (2, 3, 8)
]

distances = floyd_warshall(4, edges)
for row in distances:
    print(row)
```

---

## ✅ 5. 시간 및 공간 복잡도

| 항목 | 복잡도 |
|------|--------|
| 시간 복잡도 | O(N³) |
| 공간 복잡도 | O(N²) |

---

## ✅ 6. 활용 사례

| 분야 | 설명 |
|------|------|
| 네트워크 라우팅 | 모든 정점 간 최단 경로 필요 시 |
| 경로 최적화 | 물류, 배차, 배달 문제 |
| 그래프 이론 | 그래프 간접 연결 거리 계산 |

---

## ✅ 7. 다익스트라 vs 플로이드-워셜

| 항목 | 다익스트라 | 플로이드-워셜 |
|------|------------|----------------|
| 목적 | 한 정점 → 모든 정점 | 모든 정점 → 모든 정점 |
| 음수 간선 | 불가능 (음수X) | 가능 (음수O, 사이클X) |
| 시간 복잡도 | O(E + V log V) | O(V³) |
| 구현 방식 | 우선순위 큐 | 동적 계획법 |

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 플로이드-워셜 알고리즘 |
| 문제 유형 | 모든 쌍 최단 경로 |
| 핵심 점화식 | dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]) |
| 시간 복잡도 | O(N³) |
| 특징 | 음수 간선 처리 가능, 간단한 구현 |

