
# 📘 동적 계획법(Dynamic Programming, DP) - 최적화된 부분 문제 풀이

---

## ✅ 1. 동적 계획법이란?

> **동적 계획법(Dynamic Programming)**은 문제를 작은 하위 문제로 나누어 풀고, 그 결과를 재활용하여 전체 문제를 해결하는 최적화 기법입니다.

- **중복되는 부분 문제**와 **최적 부분 구조**가 있는 문제에 유용
- 동일한 계산을 반복하지 않도록 **메모이제이션(Memoization)** 또는 **테이블(Tabulation)** 방식 사용

---

## ✅ 2. 핵심 조건

| 조건 | 설명 |
|------|------|
| Overlapping Subproblems | 동일한 하위 문제가 반복해서 나타남 |
| Optimal Substructure | 하위 문제의 최적해가 전체 문제의 최적해로 이어짐 |

---

## ✅ 3. 동작 방식

- **Top-Down (재귀 + 메모이제이션)**: 큰 문제를 작은 문제로 나누며 재귀 호출
- **Bottom-Up (반복 + 테이블)**: 작은 문제부터 차례로 풀어 나가며 결과 누적

---

## ✅ 4. 예제 1: 피보나치 수열

### Top-Down 방식

```python
def fib(n, memo={}):
    if n <= 1:
        return n
    if n not in memo:
        memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```

### Bottom-Up 방식

```python
def fib(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

---

## ✅ 5. 예제 2: 0-1 Knapsack Problem

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n+1):
        for w in range(capacity + 1):
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i-1][w],
                               dp[i-1][w - weights[i-1]] + values[i-1])
            else:
                dp[i][w] = dp[i-1][w]
    return dp[n][capacity]
```

---

## ✅ 6. 예제 3: 최소 동전 거스름돈

```python
def min_coins(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0

    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] != float('inf') else -1
```

---

## ✅ 7. 시간 복잡도

- 문제 유형에 따라 다양 (O(n), O(n^2), O(nW) 등)
- 메모리 사용량도 크므로 최적화 필요

---

## ✅ 8. 주의사항 및 팁

- **부분 문제를 어떻게 나눌 것인가**가 핵심
- 큰 입력에 대해 **메모이제이션 vs 테이블 방식** 선택 필요
- 구현 전에 **점화식(formula)**부터 명확히 정의

---

## 🎯 마무리 요약

| 항목 | 설명 |
|------|------|
| 알고리즘명 | 동적 계획법 (Dynamic Programming) |
| 적용 조건 | 부분 문제의 중복, 최적 부분 구조 |
| 핵심 전략 | 결과 저장 및 재활용 |
| 구현 방식 | Top-Down (재귀), Bottom-Up (반복) |
| 활용 예시 | 피보나치, 배낭, 거스름돈, 최대 부분 수열 |

