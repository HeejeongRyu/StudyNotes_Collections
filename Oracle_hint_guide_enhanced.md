
# 📘 Oracle SQL 힌트(HINT) 가이드 + 사용 이유

Oracle SQL에서 **힌트(HINT)**는 옵티마이저에게 쿼리를 어떻게 실행할지 직접 지시하는 **옵션 주석**입니다.  
쿼리 성능을 향상시키기 위한 강력한 도구지만, 잘못 사용하면 오히려 성능이 저하될 수 있습니다.

---

## ✅ 1. 힌트 기본 문법

```sql
SELECT /*+ 힌트 */ 컬럼명 FROM 테이블 WHERE 조건;
```

예:
```sql
SELECT /*+ INDEX(EMP IDX_EMP_DEPTNO) */ * 
FROM EMP 
WHERE DEPTNO = 10;
```

---

## ✅ 2. 자주 사용하는 힌트와 사용 이유

### 🔹 INDEX(table_alias index_name)
> **지정한 인덱스를 강제로 사용하게 함**

```sql
SELECT /*+ INDEX(E IDX_EMP_DEPTNO) */ * 
FROM EMP E 
WHERE DEPTNO = 10;
```

- 옵티마이저가 인덱스를 무시하고 FULL SCAN할 때 강제로 인덱스 사용
- 복합 인덱스를 일부 조건에서 쓰게 하거나, 옵티마이저가 오판하는 경우에 사용

---

### 🔹 FULL(table_alias)
> **테이블 전체를 스캔하게 유도**

```sql
SELECT /*+ FULL(E) */ * 
FROM EMP E 
WHERE DEPTNO = 10;
```

- 소량의 데이터가 아닌 **대부분의 행을 읽어야 할 때 더 빠를 수 있음**
- 인덱스가 조각화되어 느릴 때도 FULL이 유리

---

### 🔹 NO_INDEX(table_alias index_name)
> **특정 인덱스를 사용하지 않게 설정**

```sql
SELECT /*+ NO_INDEX(E IDX_EMP_DEPTNO) */ * 
FROM EMP E 
WHERE DEPTNO = 10;
```

- 특정 인덱스가 비효율적일 때, 옵티마이저가 그것을 선택하지 않도록 유도

---

### 🔹 USE_NL(table_alias)
> **Nested Loop Join을 사용하도록 지시**

```sql
SELECT /*+ USE_NL(E D) */ * 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;
```

- 소규모 조인 또는 인덱스 기반 조인 시 유리
- 반복적으로 소량 데이터 조회할 때 빠름

---

### 🔹 USE_HASH(table_alias)
> **Hash Join을 사용하도록 지시**

```sql
SELECT /*+ USE_HASH(E D) */ * 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;
```

- 대량의 데이터를 조인할 때 유리
- 조인 대상이 클수록 효과적 (정렬/인덱스 없이도 빠름)

---

### 🔹 LEADING(table_alias)
> **조인의 시작 순서를 지정**

```sql
SELECT /*+ LEADING(E D) */ * 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;
```

- 옵티마이저의 조인 순서가 비효율적일 때 수동으로 조정
- 선행 테이블에 조건이 많거나 인덱스 필터가 잘 되는 경우 유리

---

### 🔹 PARALLEL(table_alias, degree)
> **병렬 처리 수행을 유도**

```sql
SELECT /*+ PARALLEL(EMP, 4) */ * 
FROM EMP;
```

- 대용량 테이블을 빠르게 처리하고자 할 때 사용
- 병렬 degree 수는 서버 리소스 고려해서 설정

---

## ✅ 3. 힌트 사용 시 주의사항

- 옵티마이저가 힌트를 **무시할 수도 있음** (비효율적이라고 판단 시)
- 최신 통계 정보가 없으면 힌트 효과가 달라짐
- 힌트는 **EXPLAIN PLAN** 또는 **DBMS_XPLAN**을 통해 반드시 실행계획을 확인해야 함
- **무분별한 힌트 사용은 성능을 오히려 저하시킬 수 있음**

---

## ✅ 4. 실행 계획 확인

```sql
EXPLAIN PLAN FOR
SELECT /*+ INDEX(EMP IDX_EMP_DEPTNO) */ * FROM EMP WHERE DEPTNO = 10;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

---

## 📌 마무리 요약

| 힌트 | 용도 요약 | 사용 추천 상황 |
|------|------------|----------------|
| INDEX | 인덱스 강제 사용 | 옵티마이저가 인덱스를 안 쓰는 경우 |
| FULL | 전체 스캔 강제 | 대부분의 행을 조회할 때 |
| NO_INDEX | 특정 인덱스 비활성화 | 불필요한 인덱스 회피 |
| USE_NL | Nested Loop Join 사용 | 인덱스 기반 소량 조인 |
| USE_HASH | Hash Join 사용 | 대용량 조인 |
| LEADING | 조인 순서 지정 | 선행 테이블이 필터링에 유리할 때 |
| PARALLEL | 병렬 처리 수행 | 대량 처리 성능 향상 필요 시 |

> 🎯 힌트는 옵티마이저의 판단을 보완해주는 "제안서"일 뿐이며, 항상 실행 계획으로 검증하는 것이 원칙입니다.
