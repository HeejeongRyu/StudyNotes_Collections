
# 📘 Oracle Nested Loop Join & USE_NL 힌트 정리

## 🧩 1. USE_NL 힌트란?

`/*+ USE_NL(table1 table2 ...) */` 힌트는 옵티마이저에게 **Nested Loop Join(NL Join)** 을 강제로 사용하도록 지시하는 힌트입니다.

```sql
SELECT /*+ USE_NL(m g c) */
       ...
FROM member m
   , guarantor g
   , corporation c
WHERE m.guarantor_no = g.no
  AND m.corp_no = c.no;
```

- `m` → `g` → `c` 순서로 **NL Join**을 사용하라는 의미입니다.
- 단, **조인 순서**까지 고정하려면 `LEADING` 힌트를 함께 사용해야 합니다.

```sql
SELECT /*+ LEADING(m g c) USE_NL(m g c) */ ...
```

---

## ⚙️ 2. Nested Loop Join (NL 조인) 개념

### 🔹 정의

NL 조인은 **중첩 반복문**처럼 작동하여 한 테이블의 각 행마다 다른 테이블을 반복적으로 검색하는 방식입니다.

### 🔹 기본 구조

```sql
FOR each row in outer_table
  FOR each row in inner_table
    IF join_condition THEN
      OUTPUT result
```

- 외부 테이블의 각 행마다 내부 테이블을 검색
- **내부 테이블에 인덱스가 있을 경우 매우 효율적**

---

## 🔍 3. 실행계획 예시

```sql
SELECT /*+ USE_NL(d) */ e.ename, d.dname
FROM emp e
JOIN dept d ON e.deptno = d.deptno;
```

### 📈 실행계획 (EXPLAIN PLAN)

```
| Id | Operation                    | Name   |
|----|------------------------------|--------|
|  0 | SELECT STATEMENT             |        |
|  1 |  NESTED LOOPS                |        |
|  2 |   TABLE ACCESS FULL          | EMP    |
|  3 |   TABLE ACCESS BY INDEX ROWID| DEPT   |
|  4 |    INDEX UNIQUE SCAN         | PK_DEPT|
```

- EMP → DEPT 순서로 NL 조인 수행
- DEPT는 인덱스를 이용하여 빠르게 검색

---

## 🧠 4. NL 조인이 유리한 경우

| 조건 | 설명 |
|------|------|
| ✅ 드라이빙 테이블의 결과가 **적을 때** | 반복 횟수가 적어 성능이 좋음 |
| ✅ 내부 테이블에 **인덱스가 존재할 때** | 빠른 접근 가능 |
| ✅ **고정된 소량 조인** | 효율적 |
| ✅ **조건이 복잡할 때** | 조건에 유연하게 대응 가능 |

---

## ❌ 5. NL 조인이 불리한 경우

| 조건 | 설명 |
|------|------|
| ❌ 드라이빙 테이블 결과가 **많을 때** | 반복 검색으로 성능 저하 |
| ❌ 내부 테이블에 **인덱스가 없을 때** | 반복적으로 전체 테이블 스캔 발생 |
| ❌ **양쪽 모두 대용량** | HASH JOIN 또는 MERGE JOIN이 더 효율적 |

---

## 🛠️ 6. 자주 사용하는 힌트 조합

| 힌트 | 기능 |
|------|------|
| `USE_NL(t1 t2)` | 지정한 테이블 간 NL 조인 사용 |
| `LEADING(t1 t2)` | 조인 순서 강제 지정 |
| `INDEX(t2 idx_name)` | 내부 테이블 인덱스 사용 강제 |

### 📝 예시

```sql
SELECT /*+ LEADING(m g c) USE_NL(m g c) INDEX(g idx_guarantor_no) INDEX(c idx_corp_no) */
       ...
FROM member m
   , guarantor g
   , corporation c
WHERE ...
```

---

## ⚖️ 7. 다른 조인 방식과 비교

| 조인 방식 | 개념 | 유리한 상황 | 리소스 사용 |
|-----------|------|--------------|---------------|
| **Nested Loop Join** | 중첩 반복문 | 소량 데이터, 인덱스 있음 | 낮음 |
| **Hash Join** | 해시 버킷 사용 | 대량 데이터, 인덱스 없음 | 메모리 높음 |
| **Merge Join** | 정렬 기반 병합 | 양 테이블이 정렬되어 있을 때 | 중간 |

---

## 📌 8. 요약

| 항목 | 내용 |
|------|------|
| 힌트 | `USE_NL(m g c)` = m → g → c 조인 시 NL 방식 강제 |
| 핵심 조건 | 드라이빙 테이블 결과가 적고, 인덱스가 내부 테이블에 있어야 효율적 |
| 자주 함께 쓰는 힌트 | `LEADING`, `INDEX` |
| 잘못 쓸 경우 | 반복적 테이블 접근으로 성능 급감 |
