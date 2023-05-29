# 트랜잭션이 만족해야 하는 ACID

### 목차

1. [트랜잭션이란?](#1-트랜잭션이란)
2. [트랜잭션의 핵심 ACID](#2-트랜잭션의-핵심-acid)
    
    2-1. [Atomic (원자성)](#1-atomic-원자성)
    
    2-2. [Consistency (일관성)](#2-consistency-일관성)
    
    2-3. [Isolation (독립성)](#3-isolation-독립성)
    
    2-4. [Durability (지속성)](#4-durability-지속성)
    
3. [결론](#3-결론)

## 1. 트랜잭션이란?

- 단일한 논리적인 작업 단위
- 논리적인 이유로 여러 SQL문들을 단일 작업으로 묶어서 나눠질 수 없게 만든 것이 트랜잭션이다.
- 트랜잭션이 SQL문들 중에 일부만 성공해서 DB에 반영되는 일은 일어나지 않는다.

## 2. 트랜잭션의 핵심 ACID

### 1. Atomic (원자성)

<img width="594" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3199f276-26aa-4985-9e85-5900772ee52f">

- **모두 성공하거나 실패하는 것**
- 트랜잭션은 논리적으로 쪼개질 수 없는 작업 단위이기 때문에 내부의 SQL문들이 모두 성공해야 한다.
- 중간에 SQL문이 실패하면 지금까지의 작업을 모두 취소하여 아무 일도 없었던 것처럼 rollback한다.

DBMS 부분

- commit 실행 시 DB에 영구적으로 저장하는 것.

개발자 부분

- 개발자는 언제 commit하거나 rollback 할지를 챙겨야 함.

### 2. Consistency (일관성)

<img width="589" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/117eac14-8610-4791-a0ae-777f030e7663">

계좌의 잔액은 0원 이상 있어야 된다는 체크사항을 걸어두어 J가 -20만원이 되어서는 안되기에 J에 업데이트문을 실행할 수 없다고 알려주고 rollback 을 실행시켜준다.

이렇게 **데이터 베이스에 정해진 룰을 잘 지켜졌을때만 commit 해주는 것을 일관성**이라고 한다.

<img width="594" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a0e2bdb3-7fbc-4c71-afa9-9d8a1c81bf35">

- 트랜잭션은 DB 상태를 consistent 상태에서 또 다른 consistent 상태로 바꿔줘야 한다.
- constraints, trigger 등을 통해 DB에 정의된 rules을 트랜잭션이 위반했다면 rollback 해야한다.

DBMS 부분

- 트랜잭션이 DB에 정의된 rule을 위반했는지는 DMBS가 commit 전에 확인하고 알려준다.

개발자 부분

- 그 외에 application 관점에서 트랜젝션이 consistent하게 동작하는지는 개발자가 챙겨야 한다.

### 3. Isolation (독립성)

<img width="581" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a786b13e-2dc9-4e61-82fe-6bad79c6002d">

아래와 같이 J가 자신의 계좌 금액 100만원을 읽은 다음 입금할 돈 20만원을 빼고 80만원을 쿼리에 썼다.

그러나, H의 계좌 금액을 읽고 J에게 받은 20만원 금액을 입금하고 쓰려는 사이 H가 ATM기를 이용해 자신의 계좌에 30만원을 입금하여 230만원이 된 상태이다..

그 이후, 200만원을 읽고 20만원 입금을 한 금액 220만원이 되어 문제가 발생되었다.

<img width="589" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f7c55de0-2984-479f-b863-a84985dde49c">

- 여러 트랜잭션들이 동시에 실행될 때도 혼자 실행되는 것처럼 동작하게 만든다.
- concurrency control의 주된 목표가 isolation이다.

DMBS 부분

- 여러 종류의 isolation level을 제공한다.

개발자 부분

- 개발자는 isolation level 중에 어떤 level로 트랜잭션을 동작시킬지 설정할 수 있다.

### 4. Durability (지속성)

<img width="589" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f3f9a671-68f1-4602-a06a-91dfd86315e1">

- **commit된 트랜잭션은 DB에 영구적으로 저장한다.**
- 즉, DB system에 문제(DB crash)가 생겨도 commit된 트랜잭션은 DB에 남아 있는다.
- ‘영구적으로 저장한다’라고 할 때는 일반적으로 ‘비휘발성 메모리(HDD, SSD,..) 에 저장함’을 의미한다.
- 기본적으로 트랜잭션의 지속성은 DBMS가 보장한다.

## 3. 결론

- 트랜잭션을 어떻게 정의해서 쓸지는 개발자가 정하는 것이다.
- 구현하려는 기능과 ACID 속성을 이해해야 트랜잭션을 잘 정의할 수 있다.
- 트랜잭션의 ACID와 관련해서 개발자가 챙겨야 하는 부분들이 있다. 즉, DBMS가 모든 것을 알아서 해주는 것은 아니다.
- 이후 isolation level에 관해서는 더 자세히 알아보고 아래 링크를 남기도록 하자.

### 참고

- https://www.youtube.com/watch?v=sLJ8ypeHGlM
