
# NVL vs NVL2 in Oracle SQL

Oracle SQL에서 `NVL`과 `NVL2`는 NULL 값을 처리하기 위한 함수입니다. 이 두 함수는 사용 목적은 비슷하지만, 동작 방식에 중요한 차이가 있습니다.

---

## 🔹 1. NVL(expr1, expr2)

- `expr1`이 **NULL이면** `expr2`를 반환  
- `expr1`이 **NULL이 아니면** `expr1`을 그대로 반환

### ✅ 예시

```sql
SELECT NVL(salary, 0) AS salary
FROM employees;
```

> salary가 NULL이면 0으로 대체, 그렇지 않으면 원래 salary 값 유지

---

## 🔹 2. NVL2(expr1, expr2, expr3)

- `expr1`이 **NULL이 아니면** `expr2`를 반환  
- `expr1`이 **NULL이면** `expr3`을 반환

### ✅ 예시

```sql
SELECT NVL2(salary, '지급', '미지급') AS status
FROM employees;
```

> salary가 NULL이 아니면 '지급', NULL이면 '미지급' 반환

---

## 🔁 차이 요약

| 항목           | NVL                       | NVL2                                |
|----------------|----------------------------|-------------------------------------|
| 인자 수        | 2개                        | 3개                                  |
| NULL일 때      | 두 번째 인자 반환          | 세 번째 인자 반환                    |
| NULL 아닐 때   | 첫 번째 인자 그대로 반환   | 두 번째 인자 반환                    |
| 사용 목적      | NULL 값의 기본값 대체      | 조건 분기 처리                        |

---

## ✅ 사용 팁

- 단순히 NULL 값을 다른 값으로 바꾸고 싶다면 `NVL`을 사용하세요.
- NULL 여부에 따라 서로 다른 로직을 적용하고 싶다면 `NVL2`가 적합합니다.
