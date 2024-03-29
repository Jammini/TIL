# 스프링 AOP(Aspect Oriented Programming)

### 목차

1. [AOP(Aspect Oriented Programming)란?](#1-aopaspect-oriented-programming란)
2. [AOP 용어](#2-aop-용어)
3. [스프링 AOP 동작과정](#3-스프링에서-aop-동작과정)

### 1. AOP(Aspect Oriented Programming)란?

AOP는 관점지향프로그래밍을 의미한다.

비즈니스 로직으로부터 중복된 관심사를 분리하는 것에 목적을 둔다. 애플리케이션에서 코드가 중복되고, 강력하게 결합되어 있어 다른 로직과 분리할 수 없는 애플리케이션 로직. ex) 로깅, 보안 트랜잭션 등등

즉, AOP란 부가기능을 따로 관리하는 것을 의미한다.

### 장점

1. 전체 코드 기반에 흩어져 있는 관심 사항이 하나의 장소로 응집한다.
2. 자신의 주요 관심사항에 대한 코드만 포함하고 있기에 코드가 깔끔해진다.

결국, 객체지향적으로 코드를 짤 수 있게 도우며 유지보수가 용이해진다.

### 2. AOP 용어

- Target : 부가기능을 부여할 대상
- Advice : 타겟에게 제공할 부가기능을 담은 모듈
- Join Point : 어드바이스가 적용될 수 있는 위치
    - 스프링 AOP에서는 메소드 실행 단계를 의미
- Pointcut : 어드바이스를 적용할 조인 포인트를 선별하는 작업
    - 스프링 AOP에서는 메소드가 Pointcut의 대상
- Advisor : 포인트컷과 어드바이스를 하나씩 갖고 있는 오브젝트
- Weaving : 조인 포인트에 어드바이스를 적용하는 방법
    - 스프링 AOP에서는 Runtime Weaving를 사용

### 3. 스프링에서 AOP 동작과정

![image](https://github.com/Jammini/TIL/assets/59176149/aa4956dd-445e-48da-ab85-878ca5e4b72b)

### BeanPostProcessor (빈 후처리기)

- 생성된 빈 객체를 스프링 컨테이너에 등록하기 전에 조작하는 객체

(1) 빈 객체를 생성한 뒤 빈 후처리기에게 전달한다.

(2) 어드바이저 내의 포인트컷(어느 메서드에 부가기능을 넣을지 선별)을 이용해 전달받은 빈이 프록시 적용 대상인지 확인한다.

(3) 프록시 생성 대상 빈들을 대상으로 프록시 객체를 생성한다.

(4) 프록시를 생성한 빈이라면 프록시 객체를, 프록시를 생성하지 않은 빈이라면 그냥 빈을 반환한다.

(5) 빈 후처리기에게 전달받은 객체를 컨테이너의 빈으로 등록한다.

### 참고

- https://www.youtube.com/watch?v=7BNS6wtcbY8