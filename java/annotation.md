# 어노테이션을 사용하는 이유와 효과에 대해 알아보자

### 목차

1. [어노테이션이란?](#1-어노테이션이란)
2. [어노테이션을 사용하는 이유와 효과](#2-어노테이션을-사용하는-이유와-효과)
3. [나만의 어노테이션 만들기](#3-나만의-어노테이션-만들기)

## 1. 어노테이션이란?

어노테이션(Annotation)은 자바 프로그래밍 언어에서 코드에 대한 메타데이터를 제공하는 일종의 표기법이다. 어노테이션은 코드의 메타데이터를 제공하고, 다양한 프레임워크와 도구에서 코드의 행동을 제어하거나 확장하는 데 널리 사용된다.

## 2. 어노테이션을 사용하는 이유와 효과

어노테이션은 다양한 목적을 제공하는데 사용하는 이유와 효과는 다음과 같다.


1. 코드 가독성 및 유지보수성 향상
- **의미 명확화:** 어노테이션을 통해 코드의 의도를 명확하게 표현할 수 있다. 예를 들어, `@Deprecated` 어노테이션은 특정 메서드가 더 이상 사용되지 않음을 명확히 나타낸다.
- **코드 분리:** 반복적인 코드나 부가적인 로직을 어노테이션으로 분리함으로써, 비즈니스 로직과 보일러플레이트 코드를 분리하여 코드의 가독성과 유지보수성을 향상시킨다.
2. 자동화 및 코드 생성
- **코드 자동 생성:** 어노테이션 처리기를 통해 반복적인 코드 생성 작업을 자동화할 수 있다. 예를 들어, `@Entity` 어노테이션을 사용하여 데이터베이스 테이블과 매핑되는 엔티티 클래스를 자동 생성할 수 있다.
- **컴파일 타임 체크:** 컴파일 시점에 어노테이션을 분석하여 코드를 검증하거나 특정 작업을 수행할 수 있다. 예를 들어, `@Override` 어노테이션을 통해 메서드가 정확히 오버라이드되는지 검증할 수 있다.
3. 런타임 로직 제어
- **의존성 주입:** `@Autowired`, `@Inject` 같은 어노테이션을 사용하여 의존성 주입 프레임워크가 객체 간의 의존성을 자동으로 설정할 수 있다.
- **애스펙트 지향 프로그래밍:** `@Transactional`, `@LogExecutionTime` 같은 어노테이션을 사용하여 특정 로직을 공통으로 적용할 수 있다. 이는 코드의 횡단 관심사를 효과적으로 관리할 수 있게 한다.


## 3. 나만의 어노테이션 만들기

어노테이션을 정의하려면 `@interface` 키워드를 사용한다. 여기서는 어노테이션의 타겟, 보존 정책등을 설정할 수 있다.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME) // 런타임에 어노테이션을 사용할 수 있도록 설정
@Target(ElementType.FIELD) // 어노테이션을 필드에 적용하도록 설정
public @interface MaskedValue {
    // 어노테이션 요소 정의 가능
}
```

### @Target

이 어노테이션은 선언한 어노테이션이 적용될 수 있는 위치를 결정한다

`@Target(ElementType.TYPE)`

**ElementType Enum에 선언된 값**

- TYPE : class, interface, enum에 적용된다.
- FIELD : 클래스 필드 변수
- METHOD : 메서드
- PARAMETER : 메서드 인자
- CONSTRUCTOR : 생성자
- LOCAL_VARIABLE : 로컬 변수
- ANNOTATION_TYPE : 어노테이션 타입에만 적용된다
- PACKAGE : 패키지
- TYPE_PARAMETER : 자바8부터 추가된 값으로 제네릭 타입 변수에 적용된다. (ex. MyClass)
- TYPE_USE : 자바8부터 추가된 값으로 어떤 타입에도 적용된다 (ex. extends, implements, 객체 생성시등등)

### @Retention

`@Retention(RetentionPolicy.RUNTIME)`

어노테이션이 어느레벨까지 유지되는지를 결정짓는다.

**RetentionPolicy Enum에 선언된 값**

- SOURCE : 자바 컴파일에 의해서 어노테이션은 삭제된다
- CLASS : 어노테이션은 .class 파일에 남아 있지만, runtime에는 제공되지 않는 어노테이션으로 Retention policy의 기본 값이다
- RUNTIME : runtime에도 어노테이션이 제공되어 자바 reflection으로 선언한 어노테이션에 접근할 수 있다

### @Inherited

이 어노테이션을 선언하면 자식클래스가 어노테이션을 상속 받는다

### @Documented

이 어노테이션을 선언하면 새로 생성한 어노테이션이 자바 문서 생성시 자바 문서에도 포함시키는 어노테이션이다.

### @Repeatable

자바8에 추가된 어노테이션으로 반복 선언을 할 수 있게 해준다.

### 참고

- [https://velog.io/@heisje/어노테이션을-사용하는-이유](https://velog.io/@heisje/%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-%EB%82%98%EB%A7%8C%EC%9D%98-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%9B%8C%EB%B0%8D%EC%97%85-%ED%81%B4%EB%9F%BD-%EC%8A%A4%ED%84%B0%EB%94%94-BE-1%EC%A3%BC%EC%B0%A8)