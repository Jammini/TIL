# 생성자주입을 선택해라!

### 목차

1. [개요](#1-개요)
2. [다양한 의존성 주입방법](#2-다양한-의존성-주입방법)
    
    2-1. [생성자 주입(Constructor Injection)](#2-1-생성자-주입constructor-injection)
    
    2-2. [수정자 주입(Setter Injection)](#2-2-수정자-주입setter-injection)
    
    2-3. [필드 주입(Field Injection)](#2-3-필드-주입field-injection)
    
3. [생성자주입을 선택하는 이유](#3-생성자주입을-선택하는-이유)
    
    3-1. [불변](#3-1-불변)
    
    3-2. [누락 방지](#3-2-누락-방지)
    
    3-3. [final 키워드](#3-3-final-키워드)
    
4. [결론](#4-결론)

## 1. 개요

스프링에서 의존관계 주입에는 생성자 주입, 필드 주입, 수정자 주입이 존재한다.

과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 스프링을 비롯한 DI프레임워크 InteliJ 등등 대부분이 생성자 주입을 권장한다.

왜 생성자 주입을 권장하는지 아래에서 살펴보자.

## 2. 다양한 의존성 주입방법

### 2-1. 생성자 주입(Constructor Injection)

생성자 주입은 생성자를 통해 의존 관계를 주입하는 방법이다.

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired
    public UserService(UserRepository userRepository, MemberService memberService) {
        this.userRepository = userRepository;
        this.memberService = memberService;
    }
    
}
```

생성자 주입은 생성자의 호출 시점에 1회 호출 되는 것이 보장된다.

그렇기 때문에 주입받은 객체가 변하지 않거나, 반드시 객체의 주입이 필요한 경우에 강제하기 위해 사용할 수 있다. 또한 Spring 프레임워크에서는 생성자 주입을 적극 지원하고 있기 때문에, 생성자가 1개만 있을 경우에 @Autowired를 생략해도 주입이 가능하도록 편의성을 제공하고 있다.

### 2-2. 수정자 주입(Setter Injection)

수정자 주입은 필드 값을 변경하는 Setter를 통해서 의존 관계를 주입하는 방법이다. Setter 주입은 생성자 주입과 다르게 주입받는 객체가 변경될 가능성이 있는 경우에 사용한다. (실제로 변경이 필요한 경우는 극히 드물다.)

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Autowired
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

@Autowired로 주입할 대상이 없는 경우에는 오류가 발생한다. 위의 예제에서는 XXX 빈이 존재하지 않을 경우에 오류가 발생하는 것이다. 주입할 대상이 없어도 동작하도록 하려면 @Autowired(required = false)를 통해 설정할 수 있다.

스프링 초기에는 수정자 주입이 자주 사용되었는데, 그 이유는 바로 getX, setX 등 프로퍼티를 기반으로 하는 자바 기본 스펙 때문이였다. 하지만 시간이 지나면서 점차 수정자 주입이 아닌 다른 방식이 주목받게 되었다.

### 2-3. 필드 주입(Field Injection)

필드 주입(Field Injection)은 필드에 바로 의존 관계를 주입하는 방법이다. IntelliJ에서 필드 인젝션을 사용하면 Field injection is not recommended이라는 경고 문구가 발생한다.

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private MemberService memberService;

}
```

필드 주입을 이용하면 코드가 간결해져서 과거에 상당히 많이 이용되었던 주입 방법이다. 하지만 필드 주입은 외부에서 접근이 불가능하다는 단점이 존재하는데, 테스트 코드의 중요성이 부각됨에 따라 필드의 객체를 수정할 수 없는 필드 주입은 거의 사용되지 않게 되었다. 또한 필드 주입은 반드시 DI 프레임워크가 존재해야 하므로 반드시 사용을 지양해야 한다. 그렇기에 애플리케이션의 실제 코드와 무관한 테스트 코드나 설정을 위해 불가피한 경우에만 이용하도록 하자.

## 3. 생성자주입을 선택하는 이유

### 3-1. 불변

위에서 생성자 주입의 특징이 불편, 필수 의존관계에 주로 사용한다고 했다. 근데 사실 대부분의 의존관계 주입은 한번 일어나면 앱 종료시점까지 의존관계를 변경할 일이 없고 없는게 좋다. 그래서 **생성자 주입이 권장**된다.

### 3-2. 누락 방지

순수 자바코드로 테스트를 할 때 생성자가 아닌 수정자 주입 방식으로 하면 의존관계 주입을 누락시킬 가능성이 있다. 생성자 주입 방식으로 하면 컴파일 오류가 미리 발생하기 때문에 오류를 미리 알 수 있다.

### 3-3. final 키워드

생성자 주입을 사용하면 `final` 키워드를 사용할 수 있다. 그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를컴파일 시점에 막아준다.

### 4. 결론

- 스프링에서 의존관계 주입은 생성자 주입을 이용하자.
- 객체의 불변성을 확보할 수 있다.
- 의존관계 주입을 누락시킬 가능성을 컴파일 오류로 통해 미리 처리 할 수 있다.
- final 키워드를 사용할 수 있다.

### 참고

- https://bellboy.tistory.com/m/21
- https://mangkyu.tistory.com/125
- [https://www.inflearn.com/course/스프링-핵심-원리-기본편/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)