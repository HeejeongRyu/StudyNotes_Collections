
# 📘 백트래킹(Backtracking) 알고리즘 - 완전 탐색을 가지치기로 최적화

---

## ✅ 1. 백트래킹이란?

> **백트래킹(Backtracking)**은 해를 찾기 위해 가능한 모든 경우를 시도해보되, 조건에 맞지 않으면 더 이상 탐색하지 않고 되돌아가는 방식의 알고리즘입니다.

- 모든 경우의 수를 탐색하지만 **불필요한 탐색은 중단 (가지치기)**
- **DFS(깊이 우선 탐색)**를 기반으로 함
- 대표 문제: **N-Queen, 순열/조합, 부분 집합, 미로 탐색 등**

---

## ✅ 2. 동작 원리

1. 어떤 해에 도달하기 위한 경로를 DFS로 탐색
2. 유망하지 않은(조건에 위배된) 경로는 더 이상 진행하지 않고 **되돌아감 (backtrack)**
3. 이 과정을 반복하여 가능한 모든 해를 탐색

---

## ✅ 3. 구조 패턴 (의사 코드)

```
def backtrack(경로, 조건):
    if 조건에 맞는 완전한 해:
        결과 저장
        return

    for 가능한 선택지:
        선택
        backtrack(다음 단계)
        선택 취소 (되돌아감)
```

---

## ✅ 4. 예제 1: 1~N까지 숫자의 모든 순열

```python
def backtrack(n, path, used):
    if len(path) == n:
        print(path)
        return

    for i in range(1, n + 1):
        if i not in used:
            used.add(i)
            backtrack(n, path + [i], used)
            used.remove(i)

backtrack(3, [], set())
```

---

## ✅ 5. 예제 2: N-Queen 문제

```python
def is_valid(queens, row, col):
    for r in range(row):
        c = queens[r]
        if c == col or abs(r - row) == abs(c - col):
            return False
    return True

def solve_n_queens(n, row=0, queens=[]):
    if row == n:
        print(queens)
        return

    for col in range(n):
        if is_valid(queens, row, col):
            solve_n_queens(n, row + 1, queens + [col])

solve_n_queens(4)
```

---

## ✅ 6. 시간 복잡도

- **최악의 경우**: 모든 경우의 수 탐색 (ex. 순열: O(n!))
- **가지치기 성능에 따라 감소**

---

## ✅ 7. 백트래킹 vs 브루트포스

| 항목 | 브루트포스 | 백트래킹 |
|------|-------------|------------|
| 탐색 방식 | 전부 탐색 | 조건 불충족 시 중단 |
| 성능 | 느림 | 빠름 (조건 설정 시) |
| 조건 필터링 | 없음 | 있음 (pruning) |

---

## ✅ 8. 주의사항 및 팁

- **조건 검사(is_valid)**를 잘 설계해야 효율적
- **사용한 값 기록 (visited/used set)** 필요
- **재귀 깊이** 고려 → `sys.setrecursionlimit()` 설정

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 백트래킹 (Backtracking) |
| 기반 | DFS |
| 핵심 개념 | 가지치기 (pruning), 재귀 |
| 활용 | N-Queen, 순열, 조합, 미로 찾기 |
| 복잡도 | O(n!) → 조건에 따라 최적화 가능 |

