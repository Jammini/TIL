# 스프링 트랜잭션 전파에 대해 알아보자

### 목차

1. [개요](#1-개요)
2. [전파 기본](#2-전파-기본)
3. [외부롤백](#3-외부-롤백)
4. [내부롤백](#4-내부-롤백)
5. [결론](#5-결론)

### 1. 개요

트랜잭션이 둘 이상 있을때 어떻게 동작하는지 자세히 알아보고, 트랜잭션 전파에 대한 개념을 알아보려고 한다. 트랜잭션을 각각 사용하는 것이 아니라 트랜잭션이 이미 진행중인데, 여기에 추가로 트랜잭션을 수행하면 어떻게 될까?

기존 트랜잭션이 별도의 트랜잭션을 진행해야 하는지? 아니면 기존 트랜잭션을 그대로 이어 받아서 트랜잭션을 수행해야 하는지? 이런 경우 어떻게 동작할지 결정하는 것을 트랜잭션 전파(propagation)라 한다.

### 2. 전파 기본

외부 트랜잭션이 수행중인데, 내부 트랜잭션이 추가로 수행됨.

<img width="781" alt="image" src="https://github.com/user-attachments/assets/500dd598-a27b-4994-bdf0-99f05e662b76">

<img width="780" alt="image" src="https://github.com/user-attachments/assets/6db60ca9-75a6-4789-8592-2e2ce9048d57">

스프링에서 이 경우 외부 트랜잭션과 내부 트랜잭션을 묶어서 하나의 트랜잭션을 만들어준다. 내부 트랜잭션이 외부 트랜잭션에 참여하는 것이다. 이것은 기본동작이고 옵션을 통해 다른 동작 방식도 선택 가능하다.

<img width="776" alt="image" src="https://github.com/user-attachments/assets/54eb91d1-ecf8-451c-8262-71a3da992b6c">

물리 트랜잭션은 우리가 이해하는 실제 데이터베이스에 적용되는 트랜잭션을 뜻한다. 실제 커넥션을 통해서 트랜잭션을 시작(setAutoCommit(false))하고, 실제 커넥션을 통해서 커밋, 롤백하는 단위이다.

**원칙**

- 모든 논리 트랜잭션이 커밋되어야 물리 트랜잭션이 커밋된다.
- 하나의 논리 트랜잭션이라도 롤백되면 물리 트랜잭션은 롤백된다.

### 3.. 외부 롤백

<img width="789" alt="image" src="https://github.com/user-attachments/assets/5025c5b1-fba8-40a1-9ebb-618cfe3bb600">


```java
@Test
 void outer_rollback() {
		log.info("외부 트랜잭션 시작");
		TransactionStatus outer = txManager.getTransaction(new DefaultTransactionAttribute());
		log.info("내부 트랜잭션 시작");
		TransactionStatus inner = txManager.getTransaction(new DefaultTransactionAttribute());
		log.info("내부 트랜잭션 커밋"); 
		txManager.commit(inner);
		log.info("외부 트랜잭션 롤백");
		txManager.rollback(outer);
}
```

```
외부 트랜잭션 시작
Creating new transaction with name [null]:
PROPAGATION_REQUIRED,ISOLATION_DEFAULT
Acquired Connection [HikariProxyConnection@461376017 wrapping conn0] for JDBC
transaction
Switching JDBC Connection [HikariProxyConnection@461376017 wrapping conn0] to
manual commit
내부 트랜잭션 시작
Participating in existing transaction
내부 트랜잭션 커밋
외부 트랜잭션 롤백
Initiating transaction rollback
Rolling back JDBC transaction on Connection [HikariProxyConnection@461376017
wrapping conn0]
Releasing JDBC Connection [HikariProxyConnection@461376017 wrapping conn0] after
transaction
```

- 외부 트랜잭션이 물리 트랜잭션을 시작하고 롤백한다.
- 내부 트랜잭션은 앞서 배운대로 직접 물리 트랜잭션에 관여하지 않는다.
- 결과적으로 외부 트랜잭션에서 시작한 물리 트랜잭션의 범위가 내부 트랜잭션까지 사용된다. 이후 외부 트랜잭션이 롤백되면서 전체 내용은 모두 롤백된다.

### 4. 내부 롤백

<img width="787" alt="image" src="https://github.com/user-attachments/assets/c9f7a14e-5d05-4318-b9c4-3c1bf312d122">

```java
@Test
 void inner_rollback() {
		log.info("외부 트랜잭션 시작");
		TransactionStatus outer = txManager.getTransaction(new DefaultTransactionAttribute());
		log.info("내부 트랜잭션 시작");
		TransactionStatus inner = txManager.getTransaction(new DefaultTransactionAttribute());
		log.info("내부 트랜잭션 커밋"); 
		txManager.rollback(inner);
		log.info("외부 트랜잭션 롤백");
		assertThatThrownBy(() -> txManager.commit(outer)).isInstanceOf(UnexpectedRollbackException.class);
}
```

실행 결과를 보면 마지막에 외부 트랜잭션을 커밋할 때 `UnexpectedRollbackException.class`가 발생하는 것을 확인할 수 있다.

```
외부 트랜잭션 시작
 Creating new transaction with name [null]:
 PROPAGATION_REQUIRED,ISOLATION_DEFAULT
 Acquired Connection [HikariProxyConnection@220038608 wrapping conn0] for JDBC
 transaction
 Switching JDBC Connection [HikariProxyConnection@220038608 wrapping conn0] to
 manual commit
내부 트랜잭션 시작
 Participating in existing transaction
내부 트랜잭션 롤백
 Participating transaction failed - marking existing transaction as rollback-only
 Setting JDBC transaction [HikariProxyConnection@220038608 wrapping conn0]
 rollback-only
외부 트랜잭션 커밋
 Global transaction is marked as rollback-only but transactional code requested
 commit
 Initiating transaction rollback
 Rolling back JDBC transaction on Connection [HikariProxyConnection@220038608
 wrapping conn0]
 Releasing JDBC Connection [HikariProxyConnection@220038608 wrapping conn0] after
transaction
```

- 논리 트랜잭션이 하나라도 롤백되면 물리 트랜잭션은 롤백된다.
- 내부 논리 트랜잭션이 롤백되면 롤백 전용 마크를 표시한다.
- 외부 트랜잭션을 커밋할 때 롤백 전용 마크를 확인한다. 롤백 전용 마크가 표시되어 있으면 물리 트랜잭션을 롤백하고,  `UnexpectedRollbackException.class`예외를 던진다.

### 5. 결론

애플리케이션 개발에서 중요한 기본 원칙은 모호함을 제거하는 것이다. 개발은 명확해야 한다.

커밋을 호출했는데 내부에서 롤백이 발생한 경우 모호하게 두면 심각한 문제가 되며 기대한 결과가 다른 경우 예외를 발생시켜서 명확하게 문제를 알려주다.

### 참고

- [https://www.inflearn.com/course/스프링-db-2/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)