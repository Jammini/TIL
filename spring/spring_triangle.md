# 스프링의 삼각형은 무엇일까?

### 목차

1. [개요](#1-개요)
2. [IoC/DI - 제어의 역전/의존성 주입](#2-iocdi---제어의-역전의존성-주입)
3. [AOP (Aspect-Oriented Programming) : 관점 지향 프로그래밍](#3-aop-aspect-oriented-programming--관점-지향-프로그래밍)
4. [PSA (Portable Service Abstraction) : 일관성 있는 서비스 추상화](#4-psa-portable-service-abstraction--일관성-있는-서비스-추상화)
5. [결론](#5-결론)

### 1. 개요

아래 그림처럼 POJO(Plain Old Java Object)를 기반으로 스프링 삼각형이라는 IoC/DI, AOP, PAS 개념이 이루고 있어 스프링 삼각형이라고 부른다. 스프링은 거대함 속에 단순함을 가지고 있는데 바로 그 단순함이 스프링 삼각형이다.

<img width="378" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f9ed029e-4317-46d7-b47e-fea95a20e860">

### **2.** IoC/DI - 제어의 역전/의존성 주입

**DI(Dependecy Injection)**

A가 B클래스를 사용하고 있을때 A가 B를 직접 생성해서 사용하는 것이 아니라 외부에서 B클래스의 인스턴스를 생성해서 주입해준다는 것이다. 

- 의존성 감소 - 사용하는 클래스에서 직접생성하는게 아니라 IOC컨테이너를 통해 생성하므로 의존성 감소한다
    - 변화에 강함
    - 재사용성이 더 좋아짐
    - 유지보수 용이
- 코드양 감소
- 테스트 용이

**IoC(Inversion of control)**

직접 의존성을 제어하는게 아닌 제어권을 역전하는 것, 즉 직접 제어하지 않는 다는 것이 IoC이다.

주로 크게 3가지 의존성 주입이 있는데 자세한 것은 아래 링크를 클릭하기를 바란다.

**IoC(DI)컨테이너**

한 객체가 어떤 객체(구체 클래스)에 의존할 것인지는 별도의 관심사이다. Spring은 의존성 주입을 도와주는 DI 컨테이너로써, 강하게 결합된 클래스들을 분리하고, 애플리케이션 실행 시점에 객체 간의 관계를 결정해 줌으로써 결합도를 낮추고 유연성을 확보해준다. 이러한 방법은 상속보다 훨씬 유연하다. 

단, 한 객체가 다른 객체를 주입받으려면 반드시 DI 컨테이너에 의해 관리되어야 한다는 것이다.

- 두 객체 간의 관계라는 관심사의 분리
- 두 객체 간의 결합도를 낮춤
- 객체의 유연성을 높임
- 테스트 작성을 용이하게 함

다양한 의존성 주입방법이 있는데 아래와 url을 통해 확인해보자.

- [생성자주입을 선택해라!](https://github.com/Jammini/TIL/blob/master/spring/constructor_injection.md)

### **3.** AOP (Aspect-Oriented Programming) : 관점 지향 프로그래밍

AOP는 관점지향프로그래밍을 의미한다.

비즈니스 로직으로부터 중복된 관심사를 분리하는 것에 목적을 둔다. 애플리케이션에서 코드가 중복되고, 강력하게 결합되어 있어 다른 로직과 분리할 수 없는 애플리케이션 로직. ex) 로깅, 보안 트랜잭션 등등

즉, AOP란 부가기능을 따로 관리하는 것을 의미한다.

스프링의 DI가 의존성의 주입이라면 AOP는 코드 주입이라고 할 수 있다. 여러 모듈을 개발하다보면 모듈들에서 공통적으로 등장하는 로직이 존재한다. 예를 들어서 입금 출금 이체와 같은 부분에서 보안적인 부분이나 트랜잭션 로그를 남기고자 하는 코드 부분들이 분명히 공통적으로 등장할 것이다.

https://github.com/Jammini/TIL/blob/master/spring/aop.md

### 4. PSA (Portable Service Abstraction) : 일관성 있는 서비스 추상화

서비스 추상화의 대표적인 예로 JDBC를 두는데 이러한 표준 스펙 덕분에 개발자는 오라클을 사용하든, MySQL을 사용하든, MS-SQL을 사용하던 어떠한 데이터베이스를 사용하던 공통된 방식으로 코드를 작성할 수 있다.

데이터베이스 종류에 관계없이 같은 방식으로 제어할 수 있는 이유는 디자인 패턴에서 설명했던 어댑터 패턴을 활용했기 때문이다.

이처럼 어댑터 패턴을 적용해 같은 일을 하는 다수의 기술을 공통의 인터페이스로 제어할 수 있게 한 것을 서비스 추상화라고 한다.

즉, 잘만든 인터페이스, 추상화가 잘된 인터페이스를 뜻한다.

### 5. 결론

- IoC/DI : 의존 역전/의존성 주입은 @Autowired나 XML 설정을 통해서 강합 결합을 느슨한 결합으로 변경해주며, 코드를 유연하게 해준다.
- AOP : 관점 지향 프로그래밍으로서 공통된 로직을 추출하여 메소드의 다양한 시점에 실행할 수 있게 해줄수 있으며, 코드를 줄여주고, 개발자가 공통 로직을 배제하고 핵심 관심사에 집중할 수 있도록 해준다.
- PSA : Portable Service Abstraction으로 일관성 있는 서비스 추상화이다. 서비스 추상화의 대표적인 예를 JDBC로 들수 있으며, 어떠한 데이터 베이스를 사용하더라도 일관성있는 방식으로 제어할 수 있도록 공통의 인터페이스를 제공하는 것이 서비스 추상화라고 한다.

### 참고

- https://dev-coco.tistory.com/83
- https://ant-programmer.tistory.com/80