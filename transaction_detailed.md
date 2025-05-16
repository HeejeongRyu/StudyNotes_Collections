
# 📘 트랜잭션(Transaction) 완전 정복

---

## ✅ 1. 트랜잭션이란?

**트랜잭션(Transaction)**은 하나의 작업 단위로 처리되는 연산의 집합입니다.  
트랜잭션은 데이터베이스에 대해 수행되는 **하나 이상의 연산**이 모두 성공하거나, 모두 실패해야 하는 **논리적 단위**입니다.

---

## ✅ 2. 트랜잭션의 목적

| 목적 | 설명 |
|------|------|
| 데이터 일관성 유지 | 여러 작업 중 일부만 반영되면 안 됨 |
| 무결성 보장 | 오류 발생 시 이전 상태로 복구 |
| 동시성 제어 | 여러 사용자가 동시에 작업해도 안정 유지 |
| 시스템 안정성 확보 | 장애 발생 시 데이터 손상 방지 |

---

## ✅ 3. 트랜잭션의 특징: ACID

| 속성 | 설명 |
|------|------|
| **A - 원자성** | 전부 성공하거나 전부 실패해야 함 |
| **C - 일관성** | 트랜잭션 전후 상태가 항상 일관된 상태 유지 |
| **I - 독립성** | 다른 트랜잭션과 독립적으로 수행되어야 함 |
| **D - 지속성** | 성공한 트랜잭션 결과는 영구적으로 저장됨 |

---

## ✅ 4. 트랜잭션 동작 흐름

```
BEGIN TRANSACTION
  → SQL 실행 (INSERT, UPDATE 등)
  → 오류 없음 → COMMIT
             → 오류 발생 → ROLLBACK
```

---

## ✅ 5. SQL 예시: 은행 계좌 이체

```sql
BEGIN;

UPDATE accounts SET balance = balance - 10000 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 10000 WHERE account_id = 2;

COMMIT;
```

---

## ✅ 6. 트랜잭션 제어 명령어

| 명령어 | 설명 |
|--------|------|
| `BEGIN` / `START TRANSACTION` | 트랜잭션 시작 |
| `COMMIT` | 변경사항 확정 |
| `ROLLBACK` | 오류 발생 시 변경사항 취소 |
| `SAVEPOINT` | 중간 저장점 설정 |
| `RELEASE SAVEPOINT` | 저장점 삭제 |
| `SET TRANSACTION` | 격리 수준 설정 |

---

## ✅ 7. 격리 수준 (Isolation Level)

| 수준 | 설명 | 현상 가능성 |
|------|------|-------------|
| READ UNCOMMITTED | 커밋 전 데이터도 읽음 | Dirty Read |
| READ COMMITTED | 커밋된 데이터만 읽음 | Non-Repeatable Read |
| REPEATABLE READ | 읽은 행은 반복해도 동일 | Phantom Read |
| SERIALIZABLE | 완전 직렬 처리 | 안전, 성능 저하 |

---

## ✅ 8. 예외 상황 예시

| 상황 | 문제 | 해결 방법 |
|------|------|------------|
| 서버 중단 | 작업 일부만 처리됨 | ROLLBACK 처리 |
| 사용자 오류 | 잘못된 조건으로 UPDATE | 재확인 로직 |
| 동시성 문제 | 충돌 발생 | 격리 수준 + Lock 사용 |

---

## ✅ 9. 트랜잭션 in Java (Spring)

```java
@Transactional
public void transfer(Account from, Account to, int amount) {
    from.withdraw(amount);
    to.deposit(amount);
    accountRepository.saveAll(List.of(from, to));
}
```

---

## ✅ 10. 실무 전략과 팁

| 전략 | 설명 |
|------|------|
| 짧고 명확한 트랜잭션 | 빠르게 처리하여 Lock 감소 |
| 예외 처리 철저히 | try-catch로 ROLLBACK 제어 |
| DB 로그 백업 | 장애 복구 대비 |
| 복잡한 로직 분리 | 여러 트랜잭션으로 나누기 |

---

## ✅ 11. 트랜잭션과 Lock

| 종류 | 설명 |
|------|------|
| Shared Lock | 읽기용, 다수 허용 |
| Exclusive Lock | 쓰기용, 단독 접근 |
| Deadlock | 트랜잭션 교착 상태 발생 |

---

## 🎯 마무리 요약

- 트랜잭션 = 작업의 최소 단위
- ACID 속성으로 안정성 확보
- 실패 → ROLLBACK, 성공 → COMMIT
- 실무에서는 격리 수준, 동시성, Lock 전략 중요
- `@Transactional`, DB 로그, 예외 처리는 실무 핵심
