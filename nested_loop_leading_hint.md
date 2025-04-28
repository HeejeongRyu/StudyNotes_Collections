
# 📘 Oracle 조인 힌트: Nested Loop Join & LEADING 힌트

이 문서는 Oracle SQL에서 자주 사용하는 조인 힌트인 `NESTED LOOP JOIN`과 `LEADING` 힌트에 대해 상세히 설명합니다.

---

## 🔹 1. Nested Loop Join (중첩 루프 조인)

### ✅ 개념

Nested Loop Join은 두 테이블을 조인할 때 **한 테이블(Outer)**의 각 행에 대해 **다른 테이블(Inner)**을 반복 탐색하는 방식입니다.

```sql
For each row in OuterTable
    Find matching rows in InnerTable
```

---

### ✅ 작동 방식 예시

```sql
SELECT *
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```

실행 계획 (예시):

```
NESTED LOOPS
  -> TABLE ACCESS FULL (DEPT)
  -> INDEX RANGE SCAN  (EMP.DEPTNO)
```

---

### ✅ 언제 유리한가?

- Outer 테이블의 **행 수가 작을 때**
- Inner 테이블에 **인덱스가 있을 때**
- **반복 검색이 빠른 경우** (소량 조인에 적합)

---

### ❌ 비효율적인 경우

- Inner 테이블이 크고 인덱스 없을 때
- Outer 테이블도 크면 반복이 많아져 **비효율**

---

### ✅ 힌트 사용 예시

```sql
SELECT /*+ USE_NL(E D) */ *
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```

---

## 🔹 2. LEADING 힌트

### ✅ 개념

`LEADING` 힌트는 옵티마이저에게 **조인 순서를 지정**해주는 힌트입니다.  
어떤 테이블을 먼저 읽을지 명시적으로 알려줍니다.

---

### ✅ 문법

```sql
SELECT /*+ LEADING(E D) */ ...
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```

- 위 힌트는 `EMP` → `DEPT` 순서로 조인을 하도록 옵티마이저에 지시

---

### ✅ 사용 이유

- 옵티마이저가 잘못된 조인 순서를 선택할 때 강제로 순서 조정
- 조건이 많은 테이블을 **먼저 필터링**할 경우 유리
- Nested Loop, Hash Join 등과 **조합 사용 가능**

---

### 🔁 힌트 조합 예시

```sql
SELECT /*+ LEADING(D) USE_NL(E) INDEX(E IDX_EMP_DEPTNO) */
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```

- `DEPT`를 먼저 읽고
- `EMP`는 `IDX_EMP_DEPTNO` 인덱스를 활용한 Nested Loop 방식으로 조인

---

## 🧾 요약

| 힌트 | 설명 | 사용 시기 |
|------|------|------------|
| `USE_NL` | Nested Loop Join 사용 | 인덱스 있고, 소량 조인 |
| `LEADING` | 조인 순서 지정 | 옵티마이저 조인 순서가 비효율적일 때 |

> 💡 옵티마이저가 항상 최적의 실행 계획을 내지는 않기 때문에, 힌트로 보정하는 것이 중요할 수 있습니다.
