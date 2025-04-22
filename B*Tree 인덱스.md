# 🌳 B*Tree 인덱스 (Balanced Tree Index)

## ✅ 개요

- Oracle에서 **가장 일반적으로 사용되는 인덱스 구조**
- "Balanced Tree"의 약자로, 트리 구조로 인덱스 노드를 구성
- 트리의 깊이가 일정하게 유지되어 **검색 속도가 일정하고 빠름**

---

## 🧱 구조 설명

B*Tree 인덱스는 크게 세 부분으로 나뉩니다:

1. **Root Block**: 인덱스의 시작점
2. **Branch Block**: 중간 경로 (분기점 역할)
3. **Leaf Block**: 실제 데이터(RowID)로 연결된 최종 노드

> 모든 리프 노드는 **수평으로 연결**되어 있어 **범위 검색**(Range Scan)에 매우 유리

---

## 🔍 특징

| 특징 | 설명 |
|------|------|
| 정렬된 구조 | 데이터가 정렬된 상태로 저장되어 탐색이 빠름 |
| 깊이 일정 | 트리의 깊이가 일정하여 탐색 성능이 안정적 |
| 빠른 검색 | =, >, <, BETWEEN, LIKE '값%' 등의 조건에 최적화 |
| 자동 균형 유지 | 새로운 값이 삽입되어도 트리 구조가 자동으로 균형 유지 |
| Null 제외 | Null 값은 인덱스에 포함되지 않음 (예외: `IS NULL` 인덱스는 별도 구성 필요) |

---

## 🛠️ 생성 예시

```sql
-- 단일 컬럼 인덱스
CREATE INDEX idx_emp_ename ON EMP(ENAME);

-- 복합 인덱스 (Composite Index)
CREATE INDEX idx_emp_dept_job ON EMP(DEPTNO, JOB);
```

🧠 결론
B*Tree 인덱스는 대부분의 OLTP 시스템에서 기본이 되는 인덱스
적절히 사용하면 조회 성능 극대화
단, DML 부하와 인덱스 남용은 주의 필요
