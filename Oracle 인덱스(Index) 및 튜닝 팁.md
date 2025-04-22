## 🔎 인덱스란?
- 데이터 조회 성능을 향상시키기 위한 객체
- 책의 목차처럼 특정 데이터를 빠르게 찾도록 도와줌
- Oracle은 기본적으로 **B*Tree 인덱스**를 사용함

---

## 📂 Oracle 인덱스 종류

| 종류 | 설명 |
|------|------|
| **B*Tree 인덱스** | 기본 인덱스, Null은 인덱싱 안 됨 |
| **Bitmap 인덱스** | 값의 종류가 적은 컬럼 (예: 성별), DML 작업이 적을 때 유리 |
| **Function-Based 인덱스** | `UPPER(name)`처럼 함수가 적용된 컬럼 대상 |
| **Composite 인덱스** | 여러 컬럼을 조합한 인덱스 |
| **Unique 인덱스** | 중복 방지를 위한 인덱스 |
| **Reverse 키 인덱스** | 높은 쓰기 충돌 방지 |
| **Invisible 인덱스** | 옵티마이저에게 숨김 처리된 인덱스 (테스트용) |

---

## 🧠 Access Path (접근 경로)

| Access Path | 설명 | 사용 조건 | 속도 |
|-------------|------|-------------|------|
| **Full Table Scan** | 테이블 전체를 읽음 | 조건 없음 또는 인덱스 부적합 | 느림 |
| **Index Unique Scan** | 인덱스를 통해 정확히 1건만 찾음 | `WHERE =` + Unique Index | 매우 빠름 |
| **Index Range Scan** | 범위 조회 (BETWEEN, LIKE 'a%') | 범위 조건 | 빠름 |
| **Index Full Scan** | 전체 인덱스를 순서대로 탐색 | `ORDER BY` 등이 인덱스 정렬과 일치할 때 | 보통 |
| **Fast Full Scan** | 인덱스 전체 탐색 + 정렬/ROWID 없음 | 인덱스만으로 결과 가능 | 빠름 |
| **Index Skip Scan** | 복합 인덱스의 첫 컬럼 없이 탐색 | 첫 컬럼 조건 없음 | 중간 |
| **Table Access by ROWID** | 인덱스로 찾은 ROWID로 테이블 접근 | 인덱스 + 테이블 조합 시 | 매우 빠름 |

---

## 🚀 인덱스 튜닝 팁

### ✅ 기본 원칙
- 자주 조회되는 컬럼
- 조건절에 자주 사용되는 컬럼
- 고유값이 많은 컬럼 (선택도 ↑)

### ✅ 실전 전략

```sql
-- 복합 인덱스
CREATE INDEX idx_emp_dept_job ON EMP(DEPTNO, JOB);

-- 함수 기반 인덱스
CREATE INDEX idx_upper_name ON EMP(UPPER(ENAME));

-- 인덱스만으로 SELECT (Index Only Scan)
SELECT ENAME FROM EMP WHERE ENAME = 'SCOTT';
```

# 📚 Oracle 인덱스(Index) 및 튜닝 팁

## 🔎 인덱스란?
- 데이터 조회 성능을 향상시키기 위한 객체
- 책의 목차처럼 특정 데이터를 빠르게 찾도록 도와줌
- Oracle은 기본적으로 **B*Tree 인덱스**를 사용함

---

## 📂 Oracle 인덱스 종류

| 종류 | 설명 |
|------|------|
| **B*Tree 인덱스** | 기본 인덱스, Null은 인덱싱 안 됨 |
| **Bitmap 인덱스** | 값의 종류가 적은 컬럼 (예: 성별), DML 작업이 적을 때 유리 |
| **Function-Based 인덱스** | `UPPER(name)`처럼 함수가 적용된 컬럼 대상 |
| **Composite 인덱스** | 여러 컬럼을 조합한 인덱스 |
| **Unique 인덱스** | 중복 방지를 위한 인덱스 |
| **Reverse 키 인덱스** | 높은 쓰기 충돌 방지 |
| **Invisible 인덱스** | 옵티마이저에게 숨김 처리된 인덱스 (테스트용) |

---

## 🧠 Access Path (접근 경로)

| Access Path | 설명 | 사용 조건 | 속도 |
|-------------|------|-------------|------|
| **Full Table Scan** | 테이블 전체를 읽음 | 조건 없음 또는 인덱스 부적합 | 느림 |
| **Index Unique Scan** | 인덱스를 통해 정확히 1건만 찾음 | `WHERE =` + Unique Index | 매우 빠름 |
| **Index Range Scan** | 범위 조회 (BETWEEN, LIKE 'a%') | 범위 조건 | 빠름 |
| **Index Full Scan** | 전체 인덱스를 순서대로 탐색 | `ORDER BY` 등이 인덱스 정렬과 일치할 때 | 보통 |
| **Fast Full Scan** | 인덱스 전체 탐색 + 정렬/ROWID 없음 | 인덱스만으로 결과 가능 | 빠름 |
| **Index Skip Scan** | 복합 인덱스의 첫 컬럼 없이 탐색 | 첫 컬럼 조건 없음 | 중간 |
| **Table Access by ROWID** | 인덱스로 찾은 ROWID로 테이블 접근 | 인덱스 + 테이블 조합 시 | 매우 빠름 |

---

## 🚀 인덱스 튜닝 팁

### ✅ 기본 원칙
- 자주 조회되는 컬럼
- 조건절에 자주 사용되는 컬럼
- 고유값이 많은 컬럼 (선택도 ↑)

### ✅ 실전 전략

```sql
-- 복합 인덱스
CREATE INDEX idx_emp_dept_job ON EMP(DEPTNO, JOB);

-- 함수 기반 인덱스
CREATE INDEX idx_upper_name ON EMP(UPPER(ENAME));

-- 인덱스만으로 SELECT (Index Only Scan)
SELECT ENAME FROM EMP WHERE ENAME = 'SCOTT';  
```
