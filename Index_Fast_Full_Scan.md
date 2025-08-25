# Index Fast Full Scan (IFFS)

## 개요
**Index Fast Full Scan(IFFS)** 은 Oracle에서 제공하는 인덱스 접근 방식 중 하나로,  
테이블 전체를 읽는 **Full Table Scan(FTS)** 과 유사하지만,  
**테이블이 아니라 인덱스 세그먼트를 다중 블록 읽기(multi-block read) 방식으로 스캔**합니다.  

핵심 특징:
- 인덱스를 **정렬 순서 없이 빠르게** 훑음
- **멀티블록 읽기 + 병렬 가능**
- **정렬 제공 불가**(ORDER BY 최적화 불가능)
- **인덱스만으로 필요한 컬럼을 만족(커버링)** 할 경우 효과 극대화
- **선택도가 낮은 조건**이나 **대량 처리**에 유리

---

## 동작 방식
- 일반 Index Range Scan은 **루트→브랜치→리프**를 따라가며 리프 블록을 순차적으로 읽음 → **정렬 순서 보장**
- IFFS는 **트리 탐색을 생략하고**, 인덱스 세그먼트 블록을 **멀티블록 읽기 방식으로 와르르** 읽어들임
- 읽은 블록에서 **필터 조건 적용**
- SELECT 컬럼이 인덱스만으로 충족되면 → **테이블 접근 없이 종료**
- 인덱스에 없는 컬럼이 필요하면 → **ROWID 랜덤 테이블 접근 추가**

---

## 선택되는 경우
1. **조건 선택도가 낮을 때**
   - 대부분의 행을 읽어야 할 때, 범위 스캔보다 인덱스 전체를 빠르게 읽는 편이 유리

2. **커버링 인덱스(인덱스-온리 쿼리)**
   - SELECT/WHERE/GROUP BY/집계에 필요한 컬럼이 인덱스에 모두 포함된 경우  
   - 테이블 접근이 생략되어 성능 극대화

3. **COUNT(*) 최적화**
   - NOT NULL 컬럼을 포함한 인덱스가 존재하면 인덱스만 스캔하여 전체 행 수 계산 가능

4. **병렬 처리**
   - `PARALLEL` 힌트와 함께 사용 시 대량 데이터 집계/검색에 강력

---

## 다른 스캔 방식과 비교

| 항목 | Index Fast Full Scan (IFFS) | Index Full Scan (IFS) | Full Table Scan (FTS) |
|---|---|---|---|
| 읽기 방식 | **멀티블록**, 병렬 가능 | **싱글블록**, 순차적 | **멀티블록**, 병렬 가능 |
| 순서 보장 | ❌ 없음 | ✅ 있음 (키 순) | ❌ 없음 |
| 주요 목적 | 대량 처리, 커버링 쿼리 | ORDER BY 최적화 | 조건 선택도 낮을 때 |
| 테이블 접근 | 필요 없을 수 있음 | 보통 필요 | 항상 필요 |

---

## 실행 계획 예시

### COUNT(*) 최적화
```sql
EXPLAIN PLAN FOR
SELECT /*+ INDEX_FFS(emp emp_ix_empno) */ COUNT(*)
FROM emp;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```
출력 예:
```
|*  1 |  SORT AGGREGATE
|   2 |   INDEX FAST FULL SCAN emp_ix_empno
```

### 커버링 쿼리
```sql
-- 인덱스: (status, updated_at, user_id)
SELECT /*+ INDEX_FFS(u ix_user_status_upd_user) */
       user_id
FROM   users u
WHERE  status IN ('A','B')
AND    updated_at >= DATE '2025-01-01';
```

---

## 힌트
- **사용 강제**: `/*+ INDEX_FFS(alias index_name) */`
- **사용 억제**: `/*+ NO_INDEX_FFS(alias index_name) */`
- **병렬**: `/*+ PARALLEL(alias, DOP) */`

---

## 주의사항
- **ORDER BY 최적화 불가** → 정렬이 필요하다면 Index Full Scan
- **커버링 아닐 경우** → 테이블 랜덤 접근 증가, 이득 감소
- **인덱스 크기 주의** → 커버링을 위해 컬럼을 과도하게 넣으면 DML 부하 증가
- **COUNT(*) 최적화** → 반드시 NOT NULL 컬럼 기반 인덱스 필요

---

## 결론
- **Index Fast Full Scan**은 **정렬을 포기하는 대신 인덱스를 빠르게 훑는 전략**  
- 인덱스 크기가 테이블보다 작고, 필요한 컬럼이 인덱스에 모두 있을 때 가장 효과적  
- 대량 처리/집계/병렬 환경에서 매우 유용
