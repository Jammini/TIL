# @Transactional을 사용할때 어떤 점을 주의할까?

### 목차

1. [개요](#1-개요)
2. [주의사항1 - 프록시 내부 호출](#2-주의사항1---프록시-내부-호출)
3. [주의사항2 - public 메서드만 트랜잭션 적용](#3-주의사항2---public-메서드만-트랜잭션-적용)
4. [주의사항3 - 초기화 시점](#4-주의사항3---초기화-시점)
5. [결론](#5-결론)

### 1. 개요

보통 트랜잭션을 걸고 싶을때 간단하게 `@Transactional` 키워드를 자주 사용한다. 이 키워드를 사용하면 스프링의 트랜잭션 AOP가 적용된다.

트랜잭션을 적용하다가 많은 개발자들이 이 문제를 실수 하는 경우가 많은데 아래 내용을 천천히 살펴보자.

### 2. 주의사항1 - 프록시 내부 호출

 `@Transactional` 을 적용하면 프록시 객체가 요청을 먼저 받아서 트랜잭션을 처리하고, 실제 객체를 호출해준다.

따라서, 트랜잭션을 적용하려면 항상 프록시를 통해서 대상 객체를 호출해야 한다.

<img width="785" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/7f7f4a49-5470-4550-912e-04e91a026c42">

AOP를 적용하면 스프링은 대상 객체 대신에 프록시를 스프링 빈으로 등록한다. 따라서 스프링은 의존관꼐 주입시에 항상 실제 객체 대신에 프록시 객체를 주입한다.

만약 **대상 객체의 내부에서 메서드 호출이 발생하면 프록시를 거치지 않고 대상 객체를 직접 호출하는 문제가 발생**한다.  `@Transactional` 이 있어도 트랜잭션이 적용되지 않기 때문에 꼭 유의하자.

```java
public void external() {
		log.info("call external");
		printTxInfo();
		internal();
}
@Transactional
public void internal() {
    log.info("call internal");
		printTxInfo();
}
```

<img width="781" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/ded56a8d-acae-424b-8541-7ea43100f97a">


메서드 내부 호출때문에 트랜잭션 프록시가 적용되지 않는 문제를 해결하기 위해서는 internal()메서드를 별도의 클래스로 분리하면 해결된다.

### 3. 주의사항2 - public 메서드만 트랜잭션 적용

스프링의 트랜잭션 AOP 기능은 public 메서드에서만 트랜잭션을 적용하도록 기본 설정이 되어있다. 그래서 protected, private, package-visible 에는 트랜잭션이 적용되지 않는다. 스프링이 이것에 대해 막아두었다.

```java
@Transactional
public class Hello {
		public method1();
		method2():
		protected method3();
		private method4();
}
```

트랜잭션은 주로 비즈니스 로직의 시작점에 걸기 때문에 대부분 외부에 열어준 곳을 시작점으로 사용한다. 그러므로 public 메서드에만 트랜잭션을 적용하도록 설정되어 있다.

```
참고: 스프링 부트 3.0
스프링 부트 3.0 부터는 protected, package-visible(default 접근제한자)에도 트랜잭션이 적용된다.
```

### 4. 주의사항3 - 초기화 시점

초기화 코드(`@PostConstruct`)와 `@Transactional` 을 함께 사용하면 트랜잭션이 적용되지 않는다.

```java
@PostConstruct
@Transactional
public void initV1() {
  log.info("Hello init @PostConstruct");
}
```

초기화 코드가 먼저 호출되고 그 다음에 트랜잭션 AOP가 적용되지 때문이다. 따라서 초기화 시점에는 해당 메서드에서 트랜잭션을 획득할 수 없다.

### 5. 결론

많은 개발자들이 트랜잭션을 적용하다가 위에 내용을 잊어버리고 실수하는 경우가 많다. 이러한 실수를 방지하기 위해 해당 내용을 정리하였고 `@Transactional` 키워드를 문제없이 사용해보자.

### 참고

- [https://www.inflearn.com/course/스프링-db-2/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)