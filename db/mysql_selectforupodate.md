# MYSQL **SELECT FOR UPDATE**

### 목차

1. [SELECT FOR UPDATE](#1-select-for-update)
2. [SELECT FOR UPDATE 의 옵션](#2-select-for-update-의-옵션)

### 1. **SELECT FOR UPDATE**

select for update는 MySQL에서 사용되는 특별한 select 문의 한 종류이다.

특정 레코드 또는 행을 select하면서 해당 레코드를 다른 트랜잭션에서 변경하지 못하도록 락을 거는 것이다.

즉 "***데이터 수정하려고 SELECT 했어. 건드리지마 !***" 와 같은 뜻이다.

**사용예시**

```sql
SELECT column1, column2, ...
FROM table_name
WHERE conditions
ORDER BY column_name
LIMIT row_count
FOR UPDATE;
```

마지막 절에 `FOR UPDATE`를 추가하면서 SELECT된 레코드들은 다른 트랜잭션에서 변경되지 않도록 락이 걸리게 된다.

따라서, 해당 트랜잭션이 커밋되거나 롤백될 때까찌 다른 트랜잭션에서는 해당 레코드들에 대한 수정이 불가능하게 된다.

### 2. **SELECT FOR UPDATE 의 옵션**

- **SELECT ~ FOR UPDATE**: 누군가가 LOCK 중이면 무한정 기다려야 한다.
- **SELECT ~ FOR UPDATE** **NOWAIT**: 누군가가 LOCK 중이면 exception 처리한다.
- **SELECT ~ FOR UPDATE** **WAIT 5**(초): 누군가가 LOCK 중이면 입력한 시간(초단위)만큼 기다렸다가 exception 처리한다.

### 참고

- [https://grandma-coding.tistory.com/entry/MySQL-SELECT-FOR-UPDATE-란](https://grandma-coding.tistory.com/entry/MySQL-SELECT-FOR-UPDATE-%EB%9E%80)
