# Oracle CBO (Cost-Based Optimizer) 실행계획 예제 정리

## 1. 옵티마이저 개요
- **옵티마이저(Optimizer)** 는 SQL 실행 시 최적의 실행계획을 선택하는 모듈.
- 종류:
  - **RBO (Rule-Based Optimizer)**: 규칙 기반 (Oracle 10g 이후 폐지됨)
  - **CBO (Cost-Based Optimizer)**: 비용 기반, **통계 정보**를 활용하여 실행계획을 선택.

---

## 2. 샘플 스키마 및 통계 수집

```sql
-- 테이블 생성
CREATE TABLE customers (
  customer_id NUMBER PRIMARY KEY,
  region      VARCHAR2(20),
  status      VARCHAR2(10)
);

CREATE TABLE orders (
  order_id     NUMBER PRIMARY KEY,
  customer_id  NUMBER NOT NULL,
  order_dt     DATE,
  amount       NUMBER,
  CONSTRAINT fk_orders_customer
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- 인덱스 생성
CREATE INDEX idx_orders_cust ON orders(customer_id);
CREATE INDEX idx_orders_dt   ON orders(order_dt);

-- 통계 수집
BEGIN
  DBMS_STATS.GATHER_TABLE_STATS(USER, 'CUSTOMERS', estimate_percent => DBMS_STATS.AUTO_SAMPLE_SIZE, method_opt => 'for all columns size auto');
  DBMS_STATS.GATHER_TABLE_STATS(USER, 'ORDERS',    estimate_percent => DBMS_STATS.AUTO_SAMPLE_SIZE, method_opt => 'for all columns size auto');
END;
/
```

실행계획 확인:

```sql
EXPLAIN PLAN FOR
SELECT o.order_id, o.amount
FROM orders o JOIN customers c ON o.customer_id = c.customer_id
WHERE c.region = 'JEJU'
  AND o.order_dt >= DATE '2025-01-01';

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY(format => 'BASIC +COST +BYTES +PREDICATE'));
```

---

## 3. 선택도(Selectivity)에 따른 실행계획 변화

### 3-1) 선택도가 높은 경우 (건수가 적음 → 인덱스 사용)

```sql
EXPLAIN PLAN FOR
SELECT o.order_id, o.amount
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE c.region = 'JEJU';
```

**예상 실행계획 요약**

```
NESTED LOOPS
 → INDEX RANGE SCAN (CUSTOMERS.region)
 → INDEX RANGE SCAN (ORDERS.customer_id)
```

> `region='JEJU'` 가 소량 → 인덱스 활용 후 Nested Loop 조인

---

### 3-2) 선택도가 낮은 경우 (건수가 많음 → 풀스캔 + 해시조인)

```sql
EXPLAIN PLAN FOR
SELECT /*+ USE_HASH(o c) */ o.order_id, o.amount
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.order_dt >= ADD_MONTHS(TRUNC(SYSDATE), -12);
```

**예상 실행계획 요약**

```
HASH JOIN
 → FULL TABLE SCAN (CUSTOMERS)
 → FULL TABLE SCAN (ORDERS)
```

> 대량 처리 → 인덱스보다 **Full Scan + Hash Join** 이 더 효율적

---

## 4. 히스토그램에 따른 실행계획 변화

### 4-1) 히스토그램 없음 (균등 분포 가정)

```sql
BEGIN
  DBMS_STATS.GATHER_TABLE_STATS(USER, 'CUSTOMERS', method_opt => 'FOR ALL COLUMNS SIZE 1');
END;
/

EXPLAIN PLAN FOR
SELECT * FROM customers WHERE region = 'JEJU';
```

**예상 실행계획 요약**

```
FULL TABLE SCAN (CUSTOMERS)
```

---

### 4-2) 히스토그램 있음 (편향 반영)

```sql
BEGIN
  DBMS_STATS.GATHER_TABLE_STATS(USER, 'CUSTOMERS',
    method_opt => 'FOR COLUMNS region SIZE AUTO');
END;
/

EXPLAIN PLAN FOR
SELECT * FROM customers WHERE region = 'JEJU';
```

**예상 실행계획 요약**

```
INDEX RANGE SCAN (CUSTOMERS.region)
```

> 데이터 편향을 반영하여 실제 건수가 적음을 인식 → 인덱스 활용

---

## 5. 조인 순서 / 방법 변경 예제

```sql
-- 작은 집합을 먼저 조인 → Nested Loop
EXPLAIN PLAN FOR
SELECT /*+ LEADING(c) USE_NL(o) */
       o.order_id, o.amount
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id
WHERE c.status = 'VIP';

-- 큰 집합 위주 처리 → Hash Join
EXPLAIN PLAN FOR
SELECT /*+ LEADING(o) USE_HASH(c) */
       o.order_id, o.amount
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.order_dt >= DATE '2025-01-01';
```

---

## 6. 바인드 변수 스니핑 이슈

- 첫 실행 시 바인드 값에 따라 실행계획이 "고정"될 수 있음.
- 이후 다른 값에서는 비효율이 발생할 수 있음.
- 해결 방법:
  - 히스토그램 생성
  - SQL Plan Baseline / SQL Profile
  - 동적 샘플링 활용

---

## 7. CBO 튜닝 핵심 체크리스트

1. **통계 최신화**: `DBMS_STATS` 활용
2. **히스토그램 관리**: 데이터 편향 컬럼에는 반드시 생성
3. **정확한 카디널리티 유지**: 인덱스 컬럼에 함수 적용 금지
4. **조인 전략 검토**:
   - 소량 처리 → Nested Loop + Index
   - 대량 처리 → Hash Join + Full Scan
5. **플랜 관리**: SQL Plan Baseline / Profile
6. **바인드 변수 처리 주의**

---

## ✅ 정리

- **CBO는 통계 정보 기반으로 최적 실행계획을 선택**
- **선택도(Selectivity), 데이터 편향, 조인 순서/방식**에 따라 실행계획이 크게 달라짐
- **정확한 통계와 히스토그램 유지**가 쿼리 튜닝의 핵심
