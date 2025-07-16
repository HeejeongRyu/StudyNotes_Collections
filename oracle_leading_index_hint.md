
# 📘 Oracle 옵티마이저 힌트: `LEADING`과 `INDEX`

---

## ✅ 1. `/*+ LEADING(t1 t2 ...) */` – 조인 순서 강제 힌트

### 🔹 개념

- 옵티마이저에게 **조인의 순서를 명시적으로 지정**하는 힌트입니다.
- 지정된 순서대로 **드라이빙 테이블 → 다음 조인 대상**으로 진행합니다.

### 🔹 문법

```sql
SELECT /*+ LEADING(t1 t2 t3) */ ...
FROM table1 t1
   , table2 t2
   , table3 t3
WHERE ...
```

- `t1` → `t2` → `t3` 순서로 조인 순서를 고정하라는 의미입니다.

### 🔹 사용 목적

- 옵티마이저는 통계 기반으로 조인 순서를 자동 선택하지만,
  때로는 **통계가 부정확하거나 예상과 다른 계획이 수립**되기도 합니다.
- 이럴 때 개발자가 **직접 조인 순서를 지정**할 수 있도록 도와주는 힌트입니다.

### 🔹 예제

```sql
SELECT /*+ LEADING(e d) USE_NL(d) */ *
FROM emp e
JOIN dept d ON e.deptno = d.deptno;
```

- `emp`를 먼저 읽고 → `dept`를 Nested Loop로 조인하라는 뜻입니다.

### 🔹 주의사항

- `LEADING`만 사용하면 조인 순서는 고정되지만 조인 방식은 고정되지 않습니다.  
  → `USE_NL`, `USE_HASH`, `USE_MERGE` 등의 힌트와 함께 사용 권장
- 지정한 순서대로 **실제로 FROM절에 포함되어 있어야 하며**, **alias가 정확히 일치**해야 합니다.

---

## ✅ 2. `/*+ INDEX(t alias index_name) */` – 인덱스 사용 강제 힌트

### 🔹 개념

- 특정 테이블 또는 alias에 대해 **특정 인덱스를 사용하라고 명시적으로 지시**하는 힌트입니다.
- 옵티마이저가 인덱스를 무시하고 풀스캔하려고 할 때, 인덱스 사용을 유도할 수 있습니다.

### 🔹 문법

```sql
SELECT /*+ INDEX(t1 index_name) */ ...
FROM table1 t1
WHERE ...
```

또는

```sql
SELECT /*+ INDEX(t1 (col1 col2)) */ ...
FROM table1 t1
WHERE ...
```

- 테이블 `t1`에 대해 `index_name` 인덱스를 강제 사용하거나,
- 특정 컬럼 기반으로 인덱스 선택을 유도할 수 있음

### 🔹 예제

```sql
SELECT /*+ INDEX(emp emp_idx_deptno) */ *
FROM emp
WHERE deptno = 10;
```

또는

```sql
SELECT /*+ INDEX(e (deptno job)) */ *
FROM emp e
WHERE deptno = 10 AND job = 'MANAGER';
```

---

## ⚠️ 힌트 사용 시 주의사항

| 항목 | 설명 |
|------|------|
| 옵티마이저는 힌트를 **"참고"**하지만 무조건 따르지는 않음 | 예: 인덱스가 매우 비효율적이면 무시 가능 |
| 힌트는 **alias** 기준으로 작동함 | `INDEX(emp emp_idx)` OK, `INDEX(EMP)` (대소문자 불일치, alias 미사용) 주의 |
| LEADING과 INDEX는 **함께 사용 시 효과 극대화** | 조인 순서 + 조인 방식 + 인덱스까지 세트로 지정 가능 |

---

## 🔄 힌트 조합 예시

```sql
SELECT /*+ LEADING(e d) USE_NL(d) INDEX(d pk_dept) */ *
FROM emp e
JOIN dept d ON e.deptno = d.deptno
WHERE e.job = 'CLERK';
```

| 힌트 | 설명 |
|------|------|
| `LEADING(e d)` | `emp` → `dept` 순서로 조인 |
| `USE_NL(d)` | `dept`를 Nested Loop로 조인 |
| `INDEX(d pk_dept)` | `dept` 테이블의 `pk_dept` 인덱스를 사용 |

---

## 📌 요약 비교

| 힌트 종류 | 기능 | 사용 예 | 효과 |
|-----------|------|---------|------|
| `LEADING(t1 t2)` | 조인 순서 고정 | `LEADING(emp dept)` | 실행계획 순서 제어 |
| `INDEX(t idx)` | 인덱스 사용 강제 | `INDEX(emp emp_idx_deptno)` | 풀스캔 방지, 인덱스 유도 |
