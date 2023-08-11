# 프록시패턴(Proxy Pattern)이란?

### 목차

1. [프록시패턴(Proxy Pattern)이란?](#1-프록시패턴proxy-pattern이란)
2. [프록시 패턴 구조](#2-프록시-패턴-구조)
3. [패턴 적용하기](#3-패턴-적용하기)
4. [장점과 단점](#4-장점과-단점)
5. [자바와 스프링에서 찾아보는 패턴](#5-자바와-스프링에서-찾아보는-패턴)

## 1. 프록시패턴(Proxy Pattern)이란?

프록시 패턴은 대상 원본 객체를 대리하여 대신 처리하게 함으로써 로직의 흐름을 제어하는 행동 패턴이다.

프록시를 번역하면 대리자, 대변인의 의미를 갖고 있다. 즉, 프록시에게 어떤 일을 대신 시키는 것이다.

객체 지향 프로그래밍에 접목해보면 클라이언트가 대상 객체를 직접 쓰는게 아니라 중간에 프록시(대리인)을 거쳐서 쓰는 코드 패턴이라고 보면 된다. 따라서 대상 객체(Subject)의 메소드를 직접 실행하는 것이 아닌, 대상 객체에 접근하기 전에 프록시(Proxy) 객체의 메서드를 접근한 후 추가적인 로직을 처리한뒤 접근하게 된다.

<img width="594" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/90fa6b03-7398-4428-a916-23517638d139">

그냥 객체를 이용하면 되지, 이렇게 번거롭게 중계 대리자를 통해 이용하는 방식을 취하는 이유는, 대상 클래스가 민감한 정보를 가지고 있거나 인스턴스화 하기에 무겁거나 추가 기능을 가미하고 싶은데, 원본 객체를 수정할수 없는 상황일 때를 극복하기 위해서다. 대체적으로 정리하자면 다음과 같은 효과를 누릴수 있다고 보면 된다.

1. **보안(Security)** : 프록시는 클라이언트가 작업을 수행할 수 있는 권한이 있는지 확인하고 검사 결과가 긍정적인 경우에만 요청을 대상으로 전달한다.
2. **캐싱(Caching)** : 프록시가 내부 캐시를 유지하여 데이터가 캐시에 아직 존재하지 않는 경우에만 대상에서 작업이 실행되도록 한다.
3. **데이터 유효성 검사(Data validation)** : 프록시가 입력을 대상으로 전달하기 전에 유효성을 검사한다.
4. **지연 초기화(Lazy initialization)** : 대상의 생성 비용이 비싸다면 프록시는 그것을 필요로 할때까지 연기할 수 있다.
5. **로깅(Logging)** : 프록시는 메소드 호출과 상대 매개 변수를 인터셉트하고 이를 기록한다.
6. **원격 객체(Remote objects)** : 프록시는 원격 위치에 있는 객체를 가져와서 로컬처럼 보이게 할 수 있다.

## 2. 프록시 패턴 구조

프록시는 다른 객체에 대한 접근을 제어하는 개체이다. 여기서 다른 객체를 **대상(Subject)**라고 부른다. 프록시와 대상은 동일한 인터페이스를 가지고 있으며 이를 통해 다른 인터페이스와 완전히 호환되도록 바꿀수 있다.

<img width="692" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/af900919-cae3-4a46-aae1-f3d65ff25c1e">

- **Subject** : Proxy와 RealSubject를 하나로 묶는 인터페이스 (다형성)
    - 대상 객체와 프록시 역할을 동일하게 하는 추상 메소드 operation() 를 정의한다.
    - 인터페이스가 있기 때문에 클라이언트는 Proxy 역할과 RealSubject 역할의 차이를 의식할 필요가 없다.
- **RealSubject** : 원본 대상 객체
- **Proxy** : 대상 객체(RealSubject)를 중계할 대리자 역할
    - 프록시는 대상 객체를 합성(composition)한다.
    - 프록시는 대상 객체와 같은 이름의 메서드를 호출하며, 별도의 로직을 수행 할수 있다 (인터페이스 구현 메소드)
    - 프록시는 흐름제어만 할 뿐 결과값을 조작하거나 변경시키면 안 된다.
- **Client** : Subject 인터페이스를 이용하여 프록시 객체를 생성해 이용.
    - 클라이언트는 프록시를 중간에 두고 프록시를 통해서 RealSubject와 데이터를 주고 받는다.

## 3. 패턴 적용하기

프록시 패턴을 적용해서 StartGame() 메소드를 실행하는데 걸리는 시간을 측정하려는 코드를 보자.

아래 코드를 보면 위의 프록시 패턴의 구조는 다음과 같이 해당된다.

Client, GameService(Subject), GameServiceProxy(Proxy), DefaultGameService(RealSubject)

```java
public class Client {
    public static void main(String[] args) {
        GameService gameService = new GameServiceProxy();
        gameService.startGame();
    }
}
```

```java
public class DefaultGameService implements GameService {
    @Override
    public void startGame() {
        System.out.println("이 자리에 오신 여러분을 진심으로 환영합니다.");
    }
}
```

```java
public interface GameService {
    void startGame();
}
```

```java
public class GameServiceProxy implements GameService {
    private GameService gameService;

    @Override
    public void startGame() {
        long before = System.currentTimeMillis();
        if (this.gameService == null) {
            this.gameService = new DefaultGameService();
        }

        gameService.startGame();
        System.out.println(System.currentTimeMillis() - before);
    }
}
```

## 4. 장점과 단점

- 장점
    - 개방 폐쇄 원칙(OCP)Visit Website 준수
        - 기존 대상 객체의 코드를 변경하지 않고 새로운 기능을 추가할 수 있다.
    - 단일 책임 원칙(SRP)Visit Website ****준수
        - 대상 객체는 자신의 기능에만 집중 하고, 그 이외 부가 기능을 제공하는 역할을 프록시 객체에 위임하여 다중 책임을 회피 할 수 있다.
    - 원래 하려던 기능을 수행하며 그외의 부가적인 작업(로깅, 인증, 네트워크 통신 등)을 수행하는데 유용하다
- 단점
    - 많은 프록시 클래스를 도입해야 하므로 코드의 복잡도가 증가한다.
        - 예를들어 여러 클래스에 로깅 기능을 추가 하고 싶다면, 동일한 코드를 적용함에도 각각의 클래스에 해당되는 프록시 클래스를 만들어서 적용해야 되기 때문에 코드량이 많아지고 중복이 발생 된다.
    - 프록시 클래스 자체에 들어가는 자원이 많다면 서비스로부터의 응답이 늦어질 수 있다.

## 5. 자바와 스프링에서 찾아보는 패턴

자바에서는 리플렉션에서 제공하는 동적 프록시(Dynamic Proxy) 기법을 이용한다.

스프링에서는 이러한 기능을 AOP를 통해 제공한다.

AOP에 대해서 자세히 살펴보려면 [링크](https://github.com/Jammini/TIL/blob/master/spring/aop.md)를 타고 확인해보자.

### 참고

- [https://inpa.tistory.com/entry/GOF-💠-프록시Proxy-패턴-제대로-배워보자](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%ED%94%84%EB%A1%9D%EC%8B%9CProxy-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)
- [https://www.inflearn.com/course/디자인-패턴/dashboard](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)