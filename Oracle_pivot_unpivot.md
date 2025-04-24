
# 📘 Oracle PIVOT / UNPIVOT 설명 및 예제

## ✅ 1. PIVOT (행 → 열 변환)

### 📖 개념
PIVOT은 특정 열의 값을 기준으로 행을 열로 바꾸고, 각 열에 대해 집계함수를 적용합니다.

---

### 🧾 예시 테이블 (sales)

| emp_id | year | sales |
|--------|------|-------|
| 1001   | 2023 | 500   |
| 1001   | 2024 | 700   |
| 1002   | 2023 | 300   |
| 1002   | 2024 | 400   |

---

### 🧑‍💻 PIVOT 쿼리

```sql
SELECT *
  FROM (
    SELECT emp_id, year, sales
      FROM sales
  )
  PIVOT (
    SUM(sales)
    FOR year IN (2023 AS y2023, 2024 AS y2024)
  );
```

---

### 🔽 결과

| emp_id | y2023 | y2024 |
|--------|-------|-------|
| 1001   | 500   | 700   |
| 1002   | 300   | 400   |

---

### 🧠 사용 포인트

- `PIVOT`은 서브쿼리 안에 있어야 함
- `SUM(sales)`은 집계 함수
- `FOR year IN (...)`은 기준 열과 변환할 열 값 지정
- `AS y2023`은 결과 열 이름 지정 (생략 가능)

---

## ✅ 2. UNPIVOT (열 → 행 변환)

### 📖 개념
UNPIVOT은 여러 열을 하나의 열로 변환합니다. 정규화나 필드명 기반 가변 데이터 처리에 유리합니다.

---

### 🧾 예시 테이블 (pivot된 데이터)

| emp_id | y2023 | y2024 |
|--------|-------|-------|
| 1001   | 500   | 700   |
| 1002   | 300   | 400   |

---

### 🧑‍💻 UNPIVOT 쿼리

```sql
SELECT *
  FROM (
    SELECT emp_id, y2023, y2024
      FROM sales_pivoted
  )
  UNPIVOT (
    sales FOR year IN (y2023 AS '2023', y2024 AS '2024')
  );
```

---

### 🔽 결과

| emp_id | year | sales |
|--------|------|-------|
| 1001   | 2023 | 500   |
| 1001   | 2024 | 700   |
| 1002   | 2023 | 300   |
| 1002   | 2024 | 400   |

---

### 🧠 사용 포인트

- `UNPIVOT`은 열 이름을 값으로 변환
- `FOR year IN (...)`에서 어떤 열을 어떤 값으로 바꿀지 명시
- `AS '2023'`은 열 이름 → 행 값 변환 시 매핑할 값

---

## 💡 추가 예시: 조건별 COUNT 피벗

```sql
SELECT *
  FROM (
    SELECT deptno, job
      FROM emp
  )
  PIVOT (
    COUNT(*) FOR job IN ('CLERK' AS clerk, 'MANAGER' AS mgr, 'ANALYST' AS analyst)
  );
```

→ 부서별로 각 직무 인원 수를 열 단위로 조회

---

## 🧾 정리

| 변환 방향 | 사용 구문 | 사용 목적 |
|-----------|-----------|------------|
| 행 → 열 | `PIVOT` | 보고서형 집계, 시각화 |
| 열 → 행 | `UNPIVOT` | 정규화, 동적 컬럼 처리 |

---
