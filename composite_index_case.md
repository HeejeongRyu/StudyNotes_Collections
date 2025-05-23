
# 📘 Oracle 복합 인덱스 설계 사례: PK + 상태값 조건

이 문서는 복합 인덱스 설계 시 `컬럼 순서`의 중요성과 실제 조건에서의 효율을 비교한 사례입니다.

---

## 🧪 시나리오

- 테이블: `TABLE1`
- 행 수: 1000개
- 컬럼:
  - `NO` → 기본키 (고유값)
  - `STATUS` → 'Y' 500개, 'N' 500개

쿼리 예시:

```sql
SELECT * 
FROM TABLE1 
WHERE NO IN (1, 3, 4236, 234, 426, 23, 1231) 
  AND STATUS = 'Y';
```

---

## ❓ 질문

> `STATUS = 'Y'`는 500건밖에 안 되니,  
> `(STATUS, NO)` 순서로 인덱스를 만들면 더 빠르지 않을까?

---

## ✅ 결론: **(NO, STATUS)** 순서가 더 효율적

| 인덱스 순서       | 동작 방식 요약                                              | 효율 |
|------------------|-------------------------------------------------------------|------|
| `(NO, STATUS)`   | NO로 빠르게 위치 지정 후, STATUS = 'Y' 필터링              | ✅ 좋음 |
| `(STATUS, NO)`   | STATUS = 'Y'로 500건 스캔 후, 그 중에서 NO 리스트 비교       | ❌ 비효율 |

---

## 🔍 이유 설명

### 1. `NO`는 고유값 → **빠르게 인덱스 타고 위치 지정 가능**

- Oracle은 `IN (...)` 조건에서도 **Index Range Scan** 또는 **Index Unique Scan** 가능
- 그 다음 STATUS = 'Y' 조건은 **Filter 단계**로 빠르게 적용됨

### 2. `STATUS`는 중복 많음 → 인덱스 필터링 성능 낮음

- STATUS='Y' 자체로는 500건 → 결과 범위를 충분히 줄이지 못함

---

## ✅ 추천 인덱스

```sql
CREATE INDEX IDX_TBL_NO_STATUS ON TABLE1(NO, STATUS);
```

> 이렇게 하면 Oracle 옵티마이저는 `NO` 조건으로 먼저 정확한 위치를 찾고,  
> 그 다음 `STATUS = 'Y'`를 필터링에 사용합니다.

---

## 📌 추가 팁: 실행계획 확인

```sql
EXPLAIN PLAN FOR
SELECT * 
FROM TABLE1 
WHERE NO IN (1, 3, 4236, 234, 426, 23, 1231) 
  AND STATUS = 'Y';

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

실행계획으로 실제 어떤 인덱스를 사용하는지 확인 가능!

---

## 🧾 요약

- `NO`는 고유값(PK)이므로 인덱스 앞에 와야 효율적
- `STATUS`는 중복 많아 인덱스 선별력 낮음
- 복합 인덱스는 `(NO, STATUS)` 순서가 더 적절
- 옵티마이저는 항상 **통계정보와 조건**을 기반으로 인덱스 선택

> 🎯 복합 인덱스는 "어떤 컬럼이 먼저 필터링에 기여할 수 있는가"를 기준으로 설계해야 합니다.
