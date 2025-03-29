# 좋아요 수를 이용해 동시성 처리 비교하기

### 목차

1. [개요](#1-개요)
2. [좋아요 설계](#2-좋아요-설계)
3. [좋아요 수 설계](#3-좋아요-수-설계)
4. [동시성 문제](#4-동시성-문제)

   4-1. [비관적 락(Pessimistic Lock)](#4-1-비관적-락pessimistic-lock)

   4-2. [낙관적 락(Optimistic Lock)](#4-2-낙관적-락optimistic-lock)

5. [비관적 락(Pessimistic Lock) vs 낙관적 락(Optimistic Lock)](#5-비관적-락pessimistic-lock-vs-낙관적-락optimistic-lock)
6. [결론](#6-결론)

### 1. 개요

좋아요 기능을 구현하면서 마주할 수 있는 문제들과 상황을 정리해보려고 한다.

특히, 좋아요 기능뿐만 아니라 데이터의 무결성을 보장하기 위해 동시성 처리를 어떻게 해야할지는 중요한 과제인데 이번 정리를 통해 차근차근 알아보자.

### 2. 좋아요 설계

기본적인 요구사항은 다음과 같다.

- 각 사용자는 게시글마다 좋아요를 누를 수 있다.
- 좋아요를 했다면 취소도 가능하다.

| id      | PK      |
|---------|---------|
| post_id | 게시글 아이디 |
| user_id | 사용자 아이디 |
| created | 생성일시    |

좋아요는 별도 테이블로 관리되며 post_id와 user_id를 이용해 조회한다.

### 3. 좋아요 수 설계

**게시글 테이블에 좋아요 수 칼럼를 넣으면 간단한거 아닌가?**

- 게시글 테이블에 좋아요 수 칼럼을 추가하고 좋아요 수를 갱신한다.

| id         | PK    |
|------------|-------|
| title      | 제목    |
| content    | 내용    |
| like_count | 좋아요 수 |

이 방법은 간단하지만 많은 요청이 들어올시 **아래와 같은 제약**이 생길 수 있다.

### Record Lock

- 특정 행(Row) 에 대한 동시 수정(Write) 충돌을 방지하기 위해 걸리는 잠금(Lock)이다.
- 여러 트랜잭션이 동시에 같은 데이터를 수정하려고 할 때, 데이터 무결성을 보장하고 경쟁 상태를 방지한다.

### Record Lock 동작 방식

- **트랜잭션 A가 특정 레코드를 수정**
    - 해당 레코드에 **잠금(Lock)** 이 걸림.
- **트랜잭션 B가 동일한 레코드를 수정 시도**
    - 트랜잭션 A가 종료될 때까지 기다림 (Blocking) 또는 오류 발생.
- **트랜잭션 A가 커밋/롤백 후 잠금 해제**
    - 트랜잭션 B가 이후 실행됨.

### Record Lock 발생 예시

<img width="702" alt="Image" src="https://github.com/user-attachments/assets/a272a3dc-5e21-487a-a0cf-352af951e8a4" />

1. 트랜잭션 A

```sql
START TRANSACTION;
UPDATE post
SET like_count = like_count + 1
WHERE id = 1;
-- 커밋하지 않고 다른 트랜잭션에서 같은 row를 수정하려 하면 대기 상태가 됨.
```

2. 트랜잭션 B → 동일한 레코드 수정 시도

```sql
START TRANSACTION;
UPDATE post
SET content = '수정한 내용 업데이트'
WHERE id = 1;
-- 첫 번째 트랜잭션이 끝날 때까지 대기 상태가 됨.
```

`B` 트랜잭션은 `A`가 `COMMIT` 또는 `ROLLBACK`을 실행할 때까지 **대기(Blocked)** 한다.

- 게시글과 좋아요 수의 변경은 Lifecycle이 다르다.
- 게시글은 작성한 사용자가 쓰기 작업을 수행한다.
    - 상대적으로 트래픽이 적음.
- 좋아요 수는 게시글을 조회한 사용자들이 쓰기 작업을 수행한다.
    - 상대적으로 트래픽이 많음.
- 서로 다른 주체에 의해서 레코드에 락이 잡힐 수 있는 것이다.
    - 게시글 쓰기와 좋아요 수 쓰기는 사용자 입장에서 독립적으로 수행되는 기능이다.

즉, Record Lock으로 인해 락을 오래 점유하면 발생하는 문제는 치명적이고 다양하다.

**그렇다면 어떻게 설계 하였는가?**

위와 같은 문제를 방지하기 위해 게시글과 좋아요 수의 변경은 독립적인 테이블로 1:1 관계를 가져 분리할 수 있다.

| post_id | PK    |
|---------|-------|
| count   | 좋아요 수 |
| version | 버저닝   |

- 게시글과 좋아요 수를 분리한다.
- 좋아요 수는 쓰기 트래픽이 비교적 크지 않다.
- 데이터의 일관성이 중요하기에 100명이 좋아요를 눌렀다면 100명이 나와야한다.
    - 관계형 데이터베이스를 이용해 좋아요 테이블의 생성/삭제와 좋아요 수 갱신을 하나의 트랜잭션으로 묶는다.

### 4. 동시성 문제

어떤 인기 게시글에 좋아요 수가 대량의 트래픽이 들어온다면 어떻게 증가/감소 처리를 해야할까?

구현 방법에 따라서 데이터가 유실 또는 중복될 수 있는 상황이 있을것이다.

<img width="351" alt="Image" src="https://github.com/user-attachments/assets/1abf8e82-e528-4488-be4e-c1411e166190" />

각 트랜잭션은 정상적으로 요청을 처리해서 commit을 했지만 2개의 요청이 좋아요 수를 갱신했기 때문에 좋아요 수는 5로 갱신 되어야 했다.

하지만 동시 요청으로 인해, 좋아요 수 증가 처리가 누락되는 상황이 발생할 수 있다는 것이다.

트랜잭션을 사용하더라고 동시성 문제로 인해, 데이터의 일관성은 깨질 수 있다.

그렇다면 동시성 문제를 해결하기 위해 어떻게 처리하면 좋을까?

### 4-1. 비관적 락(Pessimistic Lock)

비관적 관점으로 데이터 접근 시에 항상 충돌이 발생할 가능성이 있다고 가정한다.

데이터를 보호하기 위해 항상 락을 걸어 다른 트랜잭션 접근을 방지한다.

**방법 1. Record Lock**

```sql
START TRANSACTION;
INSERT INTO post_like
VALUES (post_id, user_id, created_at);
-- 좋아요 데이터 삽입
UPDATE like_count
SET count = count + 1
WHERE id = 1;
-- 좋아요 수 데이터 갱신, Lock 획득
COMMIT;
-- Lock 해제
```

데이터베이스에 저장된 데이터 기준으로 UPDATE문을 수행한다.

**방법 2. for update**

```sql
START TRANSACTION;
INSERT INTO post_like
VALUES (post_id, user_id, created_at);
-- 좋아요 데이터 삽입
SELECT *
FROM like_count
WHERE post_id = 1 FOR UPDATE;
-- for update 구문으로 데이터를 조회
-- 조회된 데이터는 Lock을 획득
UPDATE like_count
SET count = updated_count
WHERE id = 1;
-- 좋아요 수 데이터 갱신, 조회된 데이터를 기반으로 좋아요 수 증가
COMMIT;
-- Lock 해제
```

for update 구문을 이용해 락은 점유한다고 명시하면서 트랜잭션에 조회된 데이터 기준으로 UPDATE문을 수행한다.

**방법 1 vs 방법 2**

- 방법 1
    - UPDATE문 수행하는 시점에 락을 점유
    - 락 점유 시간이 상대적으로 짧음
    - DB의 현재 저장된 데이터 기준으로 증감 처리하기에 SQL문 직접 전송
- 방법 2
    - 데이터 조회 시점부터 락을 점유
    - 방법 2는 조회한 뒤 중간 과정을 수행해야 하기 때문에 락 해제가 지연 될 수 있다.
    - JPA 사용의 경우, 엔티티를 이용해 객체지향스럽게 개발할 수 있다.

### 4-2. 낙관적 락(Optimistic Lock)

낙관적 관점으로 데이터 접근시 항상 충돌이 발생할 가능성이 없다고 가정한다.

데이터의 변경 여부를 확인하여 충돌을 처리한다.

데이터가 다른 트랜잭션에 의해 수정되었는지 확인해서 수정된 내역이 있으면 rollback이나 재처리 등을 해준다.

<img width="494" alt="Image" src="https://github.com/user-attachments/assets/7d125d75-77e5-4f40-a42e-f12dadc9bdec" />

요청 1에서는 count를 증가하고 갱신할때 조회했던 version과 동일하기 때문에 정상적으로 데이터 변경이 성공했다. 하지만, 요청 2에서는 요청 1에서 version을 이미
증가 시켰으므로 충돌이 발생한다.

### 5. 비관적 락(Pessimistic Lock) vs 낙관적 락(Optimistic Lock)

비관적 락은 락을 명시적으로 잡아서 처리해야 하고 쓰기 트래픽이 단일 레코드에 대한 잠깐의 락은 크게 문제가 되지 않을 것이다.

낙과적 락은 락을 잡지 않기 때문에 지연은 낮을 수 있지만 애플리케이션에서 추가적인 재시도나 rollback 처리등이 필요하다.

위에서 설명한 것들을 구현하고 테스트코드를 이용해 각각을 시간을 측정해보았다.

```java

@Test
void likePerformanceTest() throws InterruptedException {
    ExecutorService executorService = Executors.newFixedThreadPool(100);
    likePerformanceTest(executorService, 1L, "pessimistic-lock-1");
    likePerformanceTest(executorService, 2L, "pessimistic-lock-2");
    likePerformanceTest(executorService, 3L, "optimistic-lock");
}

void likePerformanceTest(ExecutorService executorService, Long postId, String lockType)
    throws InterruptedException {

    CountDownLatch latch = new CountDownLatch(3000);
    System.out.println(lockType + " start");

    like(postId, 1L, lockType);

    long start = System.nanoTime();
    for (int i = 0; i < 3000; i++) {
        long userId = i + 2;
        executorService.submit(() -> {
            like(postId, userId, lockType);
            latch.countDown();
        });
    }
    latch.await();

    long end = System.nanoTime();
    System.out.println("lockType = " + lockType + ", time = " + (end - start) / 1000000 + "ms");
    System.out.println(lockType + " end");

    Long count = restClient.get()
                     .uri("/likes/posts/{postId}/count", postId)
                     .retrieve()
                     .body(Long.class);

    System.out.println("count = " + count);
}

void like(Long postId, Long userId, String lockType) {
    restClient.post()
        .uri("/likes/posts/{postId}/users/{userId}/" + lockType, postId, userId)
        .retrieve();
}
```

1. **pessimistic-lock-1 (Record Lock)**

- count = 3001 (좋아요 수 정상)
- time = 808ms

2. **pessimistic-lock-2 (for update)**

- count = 3001 (좋아요 수 정상)
- time = 1126ms

3. **optimistic-lock**

- count = 1742 (좋아요 수 비정상)
- time = 536ms

세가지를 비교해본 결과 다음과 같은 결과를 얻을 수 있다.

- 데이터 무결성을 보장하는가?
    - pessimistic-lock-1, pessimistic-lock-2
- 지연시간이 제일 적은건 무엇인가?
    - optimistic-lock > pessimistic-lock-1 > pessimistic-lock-2

### 6. 결론

데이터의 일관성이 중요하다면 동시성 문제를 해결하기 위한 방법을 여러가지 생각해볼 수 있다.

비관적 락, 낙관적 락 등을 이용해 동시성을 해결하면서 장단점을 비교해보았다.

데이터들의 무결성을 보장해야 한다면 비관적 락을 통해 데이터 수정전 락을 거는 방식을 적용하고 락을 사용하지 않고 성능적으로 영향을 덜 받고 싶다면 낙관적 락을 이용하면 좋을
것이다.

결론적으로, 프로젝트 규모와 상황에 맞게 최적의 동시성 제어 방법을 선택하는 것이 중요하다!
