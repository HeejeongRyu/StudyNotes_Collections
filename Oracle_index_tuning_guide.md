
# 📘 Oracle 인덱스 설정 시 고려사항 정리

인덱스(Index)는 Oracle DB에서 SQL 성능을 개선하기 위한 핵심 도구입니다. 하지만 잘못된 인덱스는 오히려 성능 저하를 유발할 수 있기 때문에, 아래의 기준에 따라 신중히 설계해야 합니다.

---

## ✅ 1. 인덱스를 설정해야 하는 경우

- WHERE 절에 자주 사용되는 컬럼
- JOIN 조건에 사용되는 컬럼
- ORDER BY 또는 GROUP BY 대상 컬럼
- SELECT로 대용량 테이블에서 소량의 데이터만 조회할 경우

---

## ✅ 2. 인덱스 설계 시 고려사항

### 🔹 (1) Selectivity (선별성)

> 중복이 적고, 다양한 값이 존재하는 컬럼일수록 인덱스 효율이 높습니다.

| 컬럼 예시 | 선별성 | 인덱스 효율 |
|-----------|--------|-------------|
| EMP_NO (사번) | 높음 | 👍 매우 좋음 |
| GENDER (M/F) | 낮음 | 👎 낮음 |
| STATUS (Y/N) | 매우 낮음 | 👎 비효율적 |

---

### 🔹 (2) 인덱스 컬럼의 순서 (복합 인덱스 시)

복합 인덱스를 만들 때는, **선별성이 높은 컬럼을 먼저 배치**하는 것이 좋습니다.

예:
```sql
CREATE INDEX IDX_EMP_DEPT_DATE ON EMP(DEPTNO, HIRE_DATE);
```
- DEPTNO가 부서 코드처럼 중복 많으면 효율 ↓
- HIRE_DATE가 선별성이 더 높다면 순서를 바꾸는 것이 좋음

---

### 🔹 (3) DML(INSERT/UPDATE/DELETE)에 미치는 영향

인덱스가 많을수록 DML 성능은 느려집니다.

- 테이블에 인덱스가 많으면, 데이터 변경 시 인덱스도 같이 갱신됨
- 잦은 변경이 있는 테이블에는 인덱스를 최소한으로 유지해야 함

---

### 🔹 (4) 함수 기반 인덱스 (Function-based Index)

WHERE 절에 함수가 자주 쓰이는 경우 인덱스 무시됨 → 함수 기반 인덱스를 따로 생성 가능

```sql
CREATE INDEX IDX_UPPER_NAME ON EMP(UPPER(NAME));
```

---

### 🔹 (5) 비트맵 인덱스 vs B*Tree 인덱스

| 구분 | B*Tree 인덱스 | 비트맵 인덱스 |
|------|---------------|----------------|
| 용도 | OLTP (읽기/쓰기 혼합) | DW/OLAP (읽기 위주) |
| 중복도 | 중복 적은 컬럼 | 중복 많은 컬럼 |
| 쓰기 성능 | 빠름 | 느림 (락 발생) |
| 예시 | 사번, 날짜 등 | 성별, 상태값 등 |

> 📌 비트맵 인덱스는 DML이 거의 없는 환경에서만 사용하세요!

---

## ✅ 3. 인덱스 관련 명령어

### 🔹 인덱스 생성

```sql
CREATE INDEX IDX_EMP_DEPT ON EMP(DEPTNO);
```

### 🔹 인덱스 삭제

```sql
DROP INDEX IDX_EMP_DEPT;
```

### 🔹 인덱스 확인

```sql
SELECT * FROM USER_INDEXES WHERE TABLE_NAME = 'EMP';
```

---

## ✅ 4. 튜닝 시 인덱스와 함께 고려해야 할 것

- 실행 계획 확인 (`EXPLAIN PLAN`, `AUTOTRACE`)
- 통계정보 최신화 (`DBMS_STATS.GATHER_TABLE_STATS`)
- 불필요한 인덱스 정리
- 복합 인덱스 또는 함수 기반 인덱스 고려
- 옵티마이저 힌트 사용 시 인덱스 활용 유도 가능

---

## 📌 마무리 요약

- 인덱스는 성능 개선의 핵심 도구지만, 잘못 설계하면 DML 부하 초래
- 반드시 **쿼리 구조와 데이터 특성**, **실행 계획**을 고려해 설계
- 중복이 적고 조회 조건에 자주 쓰이는 컬럼에 적용

> 🔧 튜닝은 "무조건 인덱스 추가"가 아니라, "필요한 곳에만 최적의 인덱스"가 원칙입니다.
