# 공유 락(shared lock) vs 배타 락(exclusive lock)

### 목차

1. [개요](#1-개요)
2. [공유 락(shared lock)](#2-공유-락shared-lock)
3. [배타 락(exclusive lock)](#3-배타-락exclusive-lock)
4. [결론](#4-결론)

### 1. 개요

DBMS에서 데이터데이 대한 동시 접근이 발생한 경우, 일관성과 무결성을 지키기 위해 해당 데이터에 잠금을 걸 수 있다. 이 때 select 가능 여부에 따라 크게 공유 락과 배타 락으로 나누게 된다.

### 2. 공유 락(shared lock)

공유 락이 걸린 데이터에 대해서는 읽기 연산(SELECT)만 실행 O, 쓰기 연산(WRITE)은 실행 X. 

공유 락을 사용하면, 조회한 데이터가 트랜잭션 내내 변경되지 않음을 보장한다.

즉, 공유 락이 걸린 데이터는 다른 트랜잭션도 똑같이 **공유 락을 획득할 수 있으나**, **배타 락은 획득할 수 없다**. 공유 락이 걸려도 읽기 작업은 가능하다는 뜻이다.

### 3. 배타 락(exclusive lock)

데이터에 대해 배타 락을 획득한 트랜잭션은, 읽기 연산(SELECT)과 쓰기 연산(WRITE)을 모두 실행 X. 

다른 트랜잭션은 배타 락이 걸린 데이터에 대해 **읽기 작업도, 쓰기 작업도 수행할 수 없다**. 

즉, 배타 락이 걸려있다면 다른 트랜잭션은 **공유 락, 배타 락 둘 다 획득 할 수 없다**. 배타 락을 획득한 트랜잭션은 해당 데이터에 대한 **독점권**을 갖는 것이다.

### 4. 결론

|  | 공유 락 | 배타 락 |
| --- | --- | --- |
| 공유 락 | 허용 | 대기 |
| 배타 락 | 대기 | 대기 |

간단하게 정리하자면 위 표와 같게 된다. 배타락이 개입하는 경우에는 트랜잭션이 락을 획득하기 위해 대기하게 되어 데드락이 발생 할 수 있게 된다.

### 참고

- https://www.youtube.com/watch?v=ZXV6ZqMyJLg
- [https://velog.io/@paki1019/공유-락Shared-Lock과-베타-락Exclusive-Lock](https://velog.io/@paki1019/%EA%B3%B5%EC%9C%A0-%EB%9D%BDShared-Lock%EA%B3%BC-%EB%B2%A0%ED%83%80-%EB%9D%BDExclusive-Lock)
- https://hudi.blog/mysql-8.0-shared-lock-and-exclusive-lock/
