
# 📘 위상 정렬 (Topological Sort)

---

## ✅ 1. 위상 정렬이란?

> 위상 정렬(Topological Sort)은 **방향 비순환 그래프(DAG: Directed Acyclic Graph)**에서 노드들을 **순서대로 나열**하는 알고리즘입니다.

- **순서가 정해진 작업**들 간의 선후 관계를 파악할 때 사용
- 예: **과목 선수과목**, **작업 스케줄링**, **빌드 순서** 등

---

## ✅ 2. 조건

- **그래프는 사이클이 없어야 함 (DAG)**
- 가능한 정렬은 여러 개일 수 있음

---

## ✅ 3. 알고리즘 설명 (Kahn’s Algorithm)

1. 진입 차수(indegree)가 0인 노드를 큐에 삽입
2. 큐에서 노드를 꺼내고 결과 리스트에 추가
3. 해당 노드와 연결된 노드의 진입 차수를 감소
4. 감소 후 진입 차수가 0이 되면 큐에 삽입
5. 큐가 빌 때까지 반복

---

## ✅ 4. 예제 (Python)

```python
from collections import deque

def topological_sort(v, edges):
    indegree = [0] * (v + 1)
    graph = [[] for _ in range(v + 1)]

    # 그래프 구성 및 진입차수 계산
    for a, b in edges:
        graph[a].append(b)
        indegree[b] += 1

    # 진입차수 0인 노드를 큐에 삽입
    queue = deque([i for i in range(1, v + 1) if indegree[i] == 0])
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return result if len(result) == v else []  # 사이클이 있는 경우 빈 리스트

# 예시 실행
v = 6
edges = [(1, 5), (2, 5), (3, 4), (4, 6), (5, 6)]
print(topological_sort(v, edges))  # 출력: [1, 2, 3, 4, 5, 6] 등의 순서
```

---

## ✅ 5. 시간 및 공간 복잡도

- **시간 복잡도**: O(V + E)
- **공간 복잡도**: O(V + E)

---

## ✅ 6. 활용 예시

| 분야 | 설명 |
|------|------|
| 빌드 시스템 | A → B → C 빌드 순서 결정 |
| 강의 계획 | 선수 과목이 있는 경우 |
| 작업 스케줄링 | 작업 간의 선후 관계 존재 시 |

---

## ✅ 7. 위상 정렬 vs 일반 DFS

| 항목 | 위상 정렬 | DFS |
|------|-----------|-----|
| 순서 기반 | 가능 | 어려움 |
| DAG 필수 여부 | 필수 | 아님 |
| 응용 | 작업 순서, 빌드 | 경로 탐색, 순회 |

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 위상 정렬 (Topological Sort) |
| 입력 | 방향 비순환 그래프 (DAG) |
| 결과 | 순서를 고려한 노드 정렬 |
| 시간 복잡도 | O(V + E) |
| 활용 | 작업 순서, 강의 계획, 컴파일 |

