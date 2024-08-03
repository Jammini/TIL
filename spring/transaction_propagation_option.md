# 트랜잭션 전파 옵션을 이용해서 복구하기

### 목차

1. [개요](#1-개요)
2. [복구 REQUIRE](#2-복구-require)
3. [복구 REQUIRE_NEW](#3-복구-require_new)
4. [복구 REQUIRES_NEW 사용X](#4-복구-requires_new-사용x)
5. [결론](#5-결론)

## 1. 개요

회원 가입과 로그를 하나의 트랜잭션으로 묶은 기능이 있다. 이때 이력 로그를 DB에 남기는 작업에 문제가 발생해서 가입 자체가 안되는 경우가 발생한다고 하자.

‘회원 가입을 시도한 로그를 남기는데, 실패하더라도 회원 가입은 유지되어야 한다.’

이 요구사항을 만족하기 위해 `REQUIRES_NEW` 옵션을 사용해보자.

## 2. 복구 REQUIRE

개발자들이 많이 하는 실수 케이스이다.

<img width="787" alt="image" src="https://github.com/user-attachments/assets/d3ebd7d7-a508-4d71-a277-b864740e4aa4">

`LogRepository` 에서 예외가 발생하면 그것은 `MemberService` 에서 예외를 잡아서 처리하면 될 것 같다라고 생각한다.

<img width="793" alt="image" src="https://github.com/user-attachments/assets/e40dd490-5a28-4296-9e0c-1aa5d45134a2">

그러나 내부 트랜잭션에서 `rollbackOnly` 를 설정하기 때문에 결과적으로 정상 흐름 처리를 해서 외부 트랜잭션에서 커밋을 호출해도 물리 트랜잭션은 롤백된다.

<img width="795" alt="image" src="https://github.com/user-attachments/assets/d8ab9ded-3df1-4371-9d9c-642944f3a8b5">


전체적인 흐름은 이와 같다.

- `LogRepository` 에서 예외가 발생한다. 예외를 던지면 `LogRepository` 의 트랜잭션 AOP가 해당 예외를 받
는다.
- 신규 트랜잭션이 아니므로 물리 트랜잭션을 롤백하지는 않고, 트랜잭션 동기화 매니저에 `rollbackOnly` 를 표
시한다.
- 이후 트랜잭션 AOP는 전달 받은 예외를 밖으로 던진다.
- 예외가 `MemberService` 에 던져지고, `MemberService` 는 해당 예외를 복구한다. 그리고 정상적으로 리턴한
다.
- 정상 흐름이 되었으므로 `MemberService` 의 트랜잭션 AOP는 커밋을 호출한다.
- 커밋을 호출할 때 신규 트랜잭션이므로 실제 물리 트랜잭션을 커밋해야 한다. 이때 `rollbackOnly` 를 체크한다.
- `rollbackOnly` 가 체크 되어 있으므로 물리 트랜잭션을 롤백한다.
- 트랜잭션 매니저는 `UnexpectedRollbackException` 예외를 던진다.
- 트랜잭션 AOP도 전달받은 `UnexpectedRollbackException` 을 클라이언트에 던진다.

## 3. 복구 REQUIRE_NEW

회원 가입을 시도한 로그를 남기는데 실패하더라도 회원 가입은 유지되어야 한다.

`REQUIRES_NEW`를 사용해 로그와 관련된 물리 트랜잭션을 별도로 분리해보자.

### 물리 트랜잭션 분리

<img width="762" alt="image" src="https://github.com/user-attachments/assets/fb9a1e54-9c57-4ec7-9585-26994c2df906">


- `MemberRepository`는 `REQUIRED`옵션을 사용해 기존 트랜잭션에 참여한다.
- `LogRepository`의 트랜잭션 옵션에 `REQUIRES_NEW`를 사용했다.
- `REQUIRES_NEW`는 항상 새로운 트랜잭션을 만든다. 따라서 해당 트랜잭션 안에서는 DB 커넥션도 별도로 사용하게 된다.

## 4. 복구 REQUIRES_NEW 사용X

<img width="813" alt="image" src="https://github.com/user-attachments/assets/8a492453-d12f-4111-9da4-fbe3a74e3741">

이런 구조로 작성하면 HTTP 요청에 동시에 2개의 커넥션을 사용하지 않는다. 순차적으로 사용하고 반환하게 된다. 

물론 구조상 `REQUIRE_NEW` 를 사용하는 것이 더 깔끔한 경우도 있으므로 각가의 장단점을 이해하고 적절하게 선택해서 사용하면 된다.

## 5. 결론

- 복구 REQUIRES을 통해 예외를 잡으면 정상 흐름이 될 것이라는 실수를 방지하자.
- REQUIRES_NEW를 사용하여 간단하게 해결할 수 있지만 connection pool의 연결을 최소 2개 가져가는 단점이 있다.
- Facade를 이용해 REQUIRES_NEW 사용하지않고 구조를 변경하는 방법도 있다.
- 두개의 장단점을 잘 비교해서 상황에 맞게 선택해서 사용하도록 하자.

### 참고

- [https://www.inflearn.com/course/스프링-db-2/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)