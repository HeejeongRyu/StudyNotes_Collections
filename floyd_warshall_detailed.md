
# 📘 플로이드-워셜 알고리즘 (Floyd-Warshall Algorithm) - 모든 쌍 최단 경로 알고리즘

---

## ✅ 1. 플로이드-워셜 알고리즘이란?

> 플로이드-워셜(Floyd-Warshall) 알고리즘은 **모든 정점 간 최단 경로**를 구하는 알고리즘입니다.

- **모든 노드에서 모든 노드까지의 최단 거리** 계산
- 음의 가중치는 허용되지만, **음의 사이클은 허용되지 않음**
- **동적 계획법(DP)** 기반으로 구현

---

## ✅ 2. 알고리즘의 아이디어

- 경유지 노드(k)를 기준으로 i → j 경로를 최적화
- 점화식:
  ```
  D[i][j] = min(D[i][j], D[i][k] + D[k][j])
  ```

---

## ✅ 3. 적용 조건

| 조건 | 설명 |
|------|------|
| 그래프 | 방향 or 무방향 가능 |
| 음의 가중치 | ✅ 가능 (단, 음의 사이클 불가) |
| 시간 복잡도 | O(n³) |

---

## ✅ 4. Python 코드 예시

```python
INF = float('inf')

def floyd_warshall(n, graph):
    distance = [[INF] * n for _ in range(n)]

    # 초기값: 자기 자신은 0, 그래프 간선은 그대로
    for i in range(n):
        for j in range(n):
            if i == j:
                distance[i][j] = 0
            elif graph[i][j] != 0:
                distance[i][j] = graph[i][j]

    # 점화식 적용
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if distance[i][j] > distance[i][k] + distance[k][j]:
                    distance[i][j] = distance[i][k] + distance[k][j]

    return distance
```

---

## ✅ 5. 입력 예시

```python
graph = [
    [0, 5, INF, 8],
    [INF, 0, 2, INF],
    [INF, INF, 0, 3],
    [INF, INF, INF, 0]
]

result = floyd_warshall(4, graph)
for row in result:
    print(row)
```

---

## ✅ 6. 시간 복잡도

- 전체 시간 복잡도: **O(n³)**
- 노드 수가 100 이하인 경우에 적합

---

## ✅ 7. 활용 분야

- 모든 정점 간 최단 거리 계산
- **도시 간 최소 이동 비용** 계산
- **최단 경로 테이블** 구축
- **경유지를 통한 최소 비용 분석**

---

## ✅ 8. 음의 사이클 판별 방법

- 플로이드-워셜 알고리즘 수행 후,
  ```
  D[i][i] < 0
  ```
  인 경우가 존재하면 → **음의 사이클 존재**

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 플로이드-워셜 |
| 시간 복잡도 | O(n³) |
| 그래프 타입 | 방향/무방향 그래프 |
| 음의 가중치 | 가능 (단, 사이클은 불가) |
| 특징 | 모든 쌍 간 최단 거리 계산 |
| 핵심 구조 | DP (동적 계획법) |

