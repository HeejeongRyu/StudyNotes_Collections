
# 📘 Oracle PL/SQL 개요 및 Java 연동 예제

## 📌 1. PL/SQL 이란?
PL/SQL은 Oracle의 SQL 확장 언어로, SQL에 절차적 제어(조건문, 반복문, 변수 선언 등)를 추가한 구조입니다.
복잡한 로직을 처리하거나 반복작업을 자동화할 때 유용합니다.

### ✅ 특징
- 변수 선언, 조건문, 반복문, 예외처리 가능
- 재사용 가능한 프로시저, 함수, 트리거 구현 가능
- SQL보다 복잡한 로직을 구현할 수 있음

---

## 📌 2. PL/SQL 기본 구조

```sql
DECLARE
   -- 변수 선언
BEGIN
   -- 실행 블록
EXCEPTION
   -- 예외 처리
END;
```

---

## 📌 3. 주요 예제

### ✅ 기본 출력

```sql
BEGIN
   DBMS_OUTPUT.PUT_LINE('Hello, PL/SQL!');
END;
```

### ✅ 변수 사용

```sql
DECLARE
   v_name VARCHAR2(50) := '홍길동';
   v_msg  VARCHAR2(100);
BEGIN
   v_msg := '안녕하세요, ' || v_name || '님!';
   DBMS_OUTPUT.PUT_LINE(v_msg);
END;
```

### ✅ 조건문

```sql
DECLARE
   v_score NUMBER := 85;
BEGIN
   IF v_score >= 90 THEN
      DBMS_OUTPUT.PUT_LINE('수');
   ELSIF v_score >= 80 THEN
      DBMS_OUTPUT.PUT_LINE('우');
   ELSE
      DBMS_OUTPUT.PUT_LINE('기타');
   END IF;
END;
```

### ✅ 반복문

```sql
DECLARE
   v_cnt NUMBER := 1;
BEGIN
   WHILE v_cnt <= 5 LOOP
      DBMS_OUTPUT.PUT_LINE('현재 숫자: ' || v_cnt);
      v_cnt := v_cnt + 1;
   END LOOP;
END;
```

### ✅ 예외 처리

```sql
DECLARE
   v_div NUMBER;
BEGIN
   v_div := 10 / 0;
EXCEPTION
   WHEN ZERO_DIVIDE THEN
      DBMS_OUTPUT.PUT_LINE('0으로 나눌 수 없습니다.');
END;
```

---

## 📌 4. 프로시저 (Procedure)

### ✅ 개념
- 반환값은 없고, 작업을 수행하는 블록
- IN, OUT, IN OUT 파라미터 사용 가능

### ✅ 예제

```sql
CREATE OR REPLACE PROCEDURE say_hello (
   p_name IN VARCHAR2
)
IS
BEGIN
   DBMS_OUTPUT.PUT_LINE('안녕하세요, ' || p_name || '님!');
END;
```

```sql
BEGIN
   say_hello('홍길동');
END;
```

---

## 📌 5. 함수 (Function)

### ✅ 개념
- 단일 값을 반환하는 블록

### ✅ 예제

```sql
CREATE OR REPLACE FUNCTION add_numbers (
   p_num1 IN NUMBER,
   p_num2 IN NUMBER
) RETURN NUMBER
IS
BEGIN
   RETURN p_num1 + p_num2;
END;
```

```sql
SELECT add_numbers(100, 200) FROM dual;
```

---

## 📌 6. 트리거 (Trigger)

### ✅ 개념
- DML 이벤트(INSERT, UPDATE, DELETE)에 반응하여 자동 실행

### ✅ 예제

```sql
CREATE OR REPLACE TRIGGER trg_emp_insert
AFTER INSERT ON emp
FOR EACH ROW
BEGIN
   DBMS_OUTPUT.PUT_LINE('직원이 추가되었습니다: ' || :NEW.ename);
END;
```

---

## 📌 7. Java에서 프로시저 호출 (OUT 파라미터 → Map)

### ✅ Oracle Procedure

```sql
CREATE OR REPLACE PROCEDURE get_emp_info (
   p_empno IN NUMBER,
   p_ename OUT VARCHAR2,
   p_sal   OUT NUMBER
)
AS
BEGIN
   SELECT ename, sal
     INTO p_ename, p_sal
     FROM emp
    WHERE empno = p_empno;
END;
```

### ✅ Java (Spring JdbcCall)

```java
SimpleJdbcCall jdbcCall = new SimpleJdbcCall(dataSource)
    .withProcedureName("get_emp_info");

Map<String, Object> inParams = new HashMap<>();
inParams.put("p_empno", 7369);

Map<String, Object> out = jdbcCall.execute(inParams);

System.out.println("이름: " + out.get("P_ENAME"));
System.out.println("급여: " + out.get("P_SAL"));
```

---

## 📌 8. Java에서 REF CURSOR 처리 (List<Map> 반환)

### ✅ Oracle Package + Procedure

```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
   TYPE emp_cursor IS REF CURSOR;
END;
/

CREATE OR REPLACE PROCEDURE get_emp_list (
   p_deptno IN NUMBER,
   p_cursor OUT emp_pkg.emp_cursor
)
AS
BEGIN
   OPEN p_cursor FOR
   SELECT empno, ename, sal
     FROM emp
    WHERE deptno = p_deptno;
END;
```

### ✅ Java (Spring JdbcCall with REF_CURSOR)

```java
SimpleJdbcCall jdbcCall = new SimpleJdbcCall(dataSource)
    .withCatalogName("EMP_PKG")
    .withProcedureName("GET_EMP_LIST")
    .declareParameters(
        new SqlParameter("P_DEPTNO", Types.INTEGER),
        new SqlOutParameter("P_CURSOR", OracleTypes.CURSOR, new ColumnMapRowMapper())
    );

Map<String, Object> inParams = new HashMap<>();
inParams.put("P_DEPTNO", 10);

Map<String, Object> result = jdbcCall.execute(inParams);

List<Map<String, Object>> empList = (List<Map<String, Object>>) result.get("P_CURSOR");

for (Map<String, Object> row : empList) {
    System.out.println("사번: " + row.get("EMPNO"));
    System.out.println("이름: " + row.get("ENAME"));
    System.out.println("급여: " + row.get("SAL"));
}
```

---

## ✅ 요약 비교표

| 구분 | 특징 | 반환값 | 사용 목적 |
|------|------|--------|------------|
| 프로시저 | 명령 블록 | ❌ | 작업 실행 중심 |
| 함수 | 계산 블록 | ✅ (RETURN) | 값 반환 중심 |
| 트리거 | 자동 실행 블록 | ❌ | 데이터 변화 감지 및 자동 처리 |
| Java 연동 | 결과를 Map 또는 List 형태로 처리 | ✅ | 데이터 처리 유연성 확보 |

---
