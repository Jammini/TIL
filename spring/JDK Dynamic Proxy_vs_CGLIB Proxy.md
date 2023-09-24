# ****JDK Dynamic Proxy vs CGLIB Proxy****

### 목차

1. [개요](#1-개요)
2. [JDK Dynamic Proxy란?](#2-jdk-dynamic-proxy란)
3. [CGLIB Proxy란?](#3-cglib-proxy란)
4. [JDK Dynamic Proxy vs CGLIB Proxy](#4-jdk-dynamic-proxy-vs-cglib-proxy)

### 1. 개요

스프링 AOP는 2가지 타입의 Proxy를 제공한다. 바로 JDK Dynamic Proxy와 CGLIB Proxy이다.

스프링 AOP의 ProxyFactoryBean이 proxy를 생성하는데, 경우에 따라 이 둘 중 하나를 서택해서 사용하는 방식이다.

### 2. JDK Dynamic Proxy란?

- JDK에서 지원하는 프록시 생성 방법
    - 외부 라이브러리에 의존하지 않는다
- 프록시 팩토리에 의해 런타임 시 다이나믹하게 만들어지는 오브젝트
- 프록시 팩토리에게 인터페이스 정보만 제공해주면 해당 인터페이스를 구현한 클래스 오브젝트를 자동으로 생성
- Reflection API를 사용한다. (느리다)
- 인터페이스가 반드시 존재해야한다
- Invocation Hanlder를 재정의한 invoke 코드를 직접 구현해줘야 부가기능이 추가된다

<img width="563" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4cc4db68-db20-4fa6-bb17-3d725b003e79">


### 3. CGLIB Proxy란?

- 프록시 팩토리에 의해 런타임 시 다이나믹하게 만들어지는 오브젝트
- 클래스 상속을 이용하여 프록시 구현. 인터페이스가 존재하지 않아도 가능
    - 바이트 코드를 조작해서 프록시 생성함
    - 인터페이스에도 강제로 적용 가능. 이 경우 클래스에도 프록시를 적용해야 한다
- Dynamic Proxy 보다 약 3배 가까이 빠르다
    - 메서드가 처음 호출되었을 때 동적으로 타깃 클래스의 바이트 코드를 조작
    - 이후 호출 시엔 조작된 바이트 코드를 재사용
- MethodInterceptor를 재정의한 intercept를 구현해야 부가 기능이 추가된다
- 메서드에 final을 붙이면 오버라이딩이 불가능

**단점**

- net.sf.cglib.proxy.Enhancer 의존성을 추가해야한다
- Default 생성자 필요
    - 없는 경우 예외 발생
- 타겟의 생성자 두번 호출

<img width="623" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/c50e7558-9286-4740-b7fd-35c2f8eef130">


### 4. JDK Dynamic Proxy vs CGLIB Proxy

처음에는 JDK Proxy가 권장되었으며 Default Proxy 생성 방식이었다.

- Java에서 기본적으로 지원하는 Proxy 클래스를 사용하는 것이기 때문에 의존성이 따로 필요없다. (CGLib 같은 경우는 net.sf.cglib.proxy.Enhancer 의존성이 필요)
- CGLib를 구현하기 위해선 반드시 NoArgs 생성자가 필요하다
- 생성된 CGLib Proxy의 메소드를 호출하면 타겟 클래스의 생성자가 2번 호출된다.

하지만 시간이 지나면서 **Spring4.3, Spring Boot 1.4부터는 CGLib 방식이 Default**가 되었는데요. 다음과 같은 개선사항이 있었습니다.

- Spring 3.2부터 CGLib가 Spring core 패키지에 들어가면서 의존성 추가필요없어짐
- Spring 4.0부터 Objensis 라이브러리의 도움을 받아 NoArgs 생성자 없이도 Proxy이 가능해지고, 생성자가 2번 호출되던 이슈도 수정

### 참고

- https://jhyonhyon.tistory.com/50?category=867755
- https://suyeonchoi.tistory.com/81
- https://www.youtube.com/watch?v=RHxTV7qFV7M