
# 📘 Oracle 실행 계획 (Execution Plan) 분석 가이드

---

## 📌 목차

1. [실행 계획이란?](#실행-계획이란)
2. [TABLE PLAN 추출 방법](#table-plan-추출-방법)
    - `EXPLAIN PLAN`
    - `AUTOTRACE`
    - `DBMS_XPLAN.DISPLAY`
3. [실행 계획 주요 키워드 설명](#실행-계획-주요-키워드-설명)
4. [실행 계획 해석 실습 예제](#실행-계획-해석-실습-예제)
5. [자주 보는 연산자 설명](#자주-보는-연산자-설명)
6. [성능 튜닝 시 주요 체크포인트](#성능-튜닝-시-주요-체크포인트)

---

## 🔍 실행 계획이란?

SQL 문장을 실행했을 때 Oracle 옵티마이저가 **어떤 방식으로 데이터를 조회할지 결정한 절차**를 나타낸 것이며, 튜닝 시 **어디서 병목이 발생하는지** 확인할 수 있는 핵심 도구입니다.

---

## 🛠️ TABLE PLAN 추출 방법

### ✅ 1. `EXPLAIN PLAN` + `DBMS_XPLAN.DISPLAY`

```sql
EXPLAIN PLAN FOR
SELECT * FROM EMP WHERE EMPNO = 7788;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

- `EXPLAIN PLAN`은 SQL 실행 **계획만 예측**해서 보여줍니다. 실제 실행하지는 않습니다.
- `DBMS_XPLAN.DISPLAY`는 결과를 예쁘게 출력합니다.

> 🔔 TIP: PLAN_TABLE이 없으면 다음 명령으로 생성  
> `@$ORACLE_HOME/rdbms/admin/utlxplan.sql`

---

### ✅ 2. `AUTOTRACE` 사용 (SQL*Plus 또는 SQL Developer)

```sql
SET AUTOTRACE ON;
SELECT * FROM EMP WHERE DEPTNO = 10;
```

- 결과와 함께 실행 계획 + 통계 정보(논리적 읽기 등)를 같이 보여줍니다.
- `SET AUTOTRACE TRACEONLY`를 사용하면 결과 없이 실행 계획만 출력.

---

### ✅ 3. 실제 실행된 PLAN 보기 (`V$SQL_PLAN` 또는 `DBMS_XPLAN.DISPLAY_CURSOR`)

```sql
SELECT * FROM V$SQL WHERE SQL_TEXT LIKE 'SELECT * FROM EMP%';

-- SQL_ID 확인 후
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY_CURSOR('SQL_ID_값', NULL, 'ALLSTATS LAST'));
```

- `DISPLAY_CURSOR`는 **실제로 실행된 실행 계획**과 통계를 보여줍니다.
- Autotrace보다 신뢰도가 높음.

---

## 🧾 실행 계획 주요 키워드 설명

| 항목 | 설명 |
|------|------|
| `Id` | 실행 순서 (낮을수록 먼저 실행됨) |
| `Operation` | 어떤 작업을 수행하는지 (TABLE ACCESS, JOIN 등) |
| `Object Name` | 사용된 테이블 또는 인덱스 이름 |
| `Rows` | 예상 행 수 |
| `Bytes` | 예상 데이터 크기 |
| `Cost` | 해당 단계까지의 누적 비용 |
| `Access Predicates` | 인덱스 접근 조건 |
| `Filter Predicates` | 데이터 필터 조건 (WHERE절 조건 등) |

---

## 🧪 실행 계획 해석 실습 예제

```sql
EXPLAIN PLAN FOR
SELECT EMP.ENAME, DEPT.DNAME
FROM EMP
JOIN DEPT ON EMP.DEPTNO = DEPT.DEPTNO
WHERE EMP.JOB = 'CLERK';

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

### 결과 예시

```
---------------------------------------------------------------------------------------
| Id | Operation                    | Name  | Rows  | Bytes | Cost (%CPU)| Time     |
---------------------------------------------------------------------------------------
|  0 | SELECT STATEMENT             |       |     1 |    50 |     4  (25)| 00:00:01 |
|  1 |  NESTED LOOPS                |       |     1 |    50 |     4  (25)| 00:00:01 |
|  2 |   TABLE ACCESS BY INDEX ROWID| EMP   |     1 |    25 |     2  (50)| 00:00:01 |
|  3 |    INDEX RANGE SCAN          | EMP_JOB_IDX | 1 |       |     1  (0) | 00:00:01 |
|  4 |   TABLE ACCESS BY INDEX ROWID| DEPT  |     1 |    25 |     2  (0) | 00:00:01 |
|  5 |    INDEX UNIQUE SCAN         | PK_DEPT | 1 |       |     1  (0) | 00:00:01 |
---------------------------------------------------------------------------------------
```

### 해석

- `INDEX RANGE SCAN`을 통해 `EMP_JOB_IDX` 인덱스를 사용하여 CLERK인 사원 조회
- 그 결과를 기반으로 `DEPT` 테이블을 `PK_DEPT` 인덱스를 사용해 JOIN
- 옵티마이저는 `NESTED LOOP JOIN` 전략 선택

---

## 📚 자주 보는 연산자 설명

| 연산자 | 설명 |
|--------|------|
| `TABLE ACCESS FULL` | 전체 테이블 스캔 (인덱스 미사용) |
| `TABLE ACCESS BY INDEX ROWID` | 인덱스로 찾고, 실제 ROW는 테이블에서 조회 |
| `INDEX RANGE SCAN` | 범위 조건 검색 (e.g., `BETWEEN`, `<`, `>` 등) |
| `INDEX UNIQUE SCAN` | 고유 인덱스를 통해 1건만 조회 |
| `NESTED LOOPS` | 외부 루프마다 내부 테이블 탐색 (소량 데이터 적합) |
| `HASH JOIN` | 큰 테이블간 조인 시 사용 |
| `MERGE JOIN` | 정렬된 결과로 조인 (성능 좋을 수 있음) |

---

## 🔍 성능 튜닝 시 주요 체크포인트

- `TABLE ACCESS FULL`이 불필요하게 발생하는가?
- 예상 행 수(Rows)가 실제보다 크게 또는 작게 예측되는가?
- 인덱스가 잘 사용되고 있는가? (Index Range Scan, Unique Scan 등)
- JOIN 방식은 적절한가? (`Nested Loops`, `Hash Join`, `Merge Join`)
- `Cost`, `Bytes`, `Rows`가 급격히 높은 단계는 병목 가능성 있음
- `FILTER`, `ACCESS PREDICATE`의 위치에 따라 인덱스 효율 차이 발생

---

## 📎 참고 명령어 모음

```sql
-- PLAN_TABLE 생성
@$ORACLE_HOME/rdbms/admin/utlxplan.sql

-- 실행 계획 보기
EXPLAIN PLAN FOR [SQL문];
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);

-- 실제 실행된 SQL 실행계획 보기
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY_CURSOR('SQL_ID', NULL, 'ALLSTATS LAST'));

-- SQL ID 확인
SELECT SQL_ID, SQL_TEXT FROM V$SQL WHERE SQL_TEXT LIKE 'SELECT * FROM EMP%';
```
