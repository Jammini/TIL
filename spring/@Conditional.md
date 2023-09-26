# 스프링에서 Conditional 어노테이션이란?

### 목차

1. [@Conditional이란?](#1-conditional이란)
2. [구현예제](#2-구현예제)
3. [다양한 기능](#3-다양한-기능)
4. [결론](#4-결론)

### 1. @Conditional이란?

Spring Framework에서 사용되는 어노테이션 중 하나로, 빈(Bean)의 조건부 생성 및 등록을 제어하기 위해 사용되는 어노테이션이다.

즉, 컴포넌트의 Bean등록여부에 조건을 달 수 있게하는 어노테이션이다.

스프링은 IoC컨테이너에 객체를 Bean으로 등록하여 사용하는데 @Component로 선언된 클래스는 모두 Bean으로 등록하고 여기에 조건부를 달 수 가 있다.

스프링 기반의 웹사이트에서 추가기능을 넣으려고 해보자.

<img width="603" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3e9bda6f-84f8-41d9-b31e-ba73d83a1616">

스프링은 추가기능의 설정파일을 읽고 POJO에서 Bean을 IoC컨테이너에 등록한다. 이때 설정파일을 조건부로 읽는다면 필요할 때만 IoC컨테이너에 Bean을 등록할 수 있다. 여기서 @Conditional 어노테이션을 사용한다.

### 2. 구현예제

@Conditional 에 대해서 자세히 알아보자. 이름 그대로 특정 조건을 만족하는가 하지 않는가를 구별하는 기능이다.

```java
package org.springframework.context.annotation;
public interface Condition {
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```

- matches() 메서드가 true 를 반환하면 조건에 만족해서 동작하고, false 를 반환하면 동작하지 않는다.
- ConditionContext : 스프링 컨테이너, 환경 정보등을 담고 있다.
- AnnotatedTypeMetadata : 애노테이션 메타 정보를 담고 있다.

Condition 인터페이스를 구현해서 다음과 같이 자바 시스템 속성이 memory=on 이라고 되어 있을 때만 메모리 기능이 동작하도록 만들어보자.

```java
#VM Options
#java -Dmemory=on -jar project.jar
```

```java
@Slf4j
public class MemoryCondition implements Condition {
	@Override
  public boolean matches(ConditionContext context, AnnotatedTypeMetadatametadata) {
			String memory = context.getEnvironment().getProperty("memory");
			log.info("memory={}", memory);
      return "on".equals(memory);
	}
}
```

- 환경 정보에 memory=on 이라고 되어 있는 경우에만 true 를 반환한다.

<img width="706" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/00e2b1b8-be87-4116-bce2-fb80bb77bbe4">


```java
@Configuration 
@Conditional(MemoryCondition.class) //추가
public class MemoryConfig {
    @Bean
    public MemoryController memoryController() {
        return new MemoryController(memoryFinder());
    }
    @Bean
    public MemoryFinder memoryFinder() {
        return new MemoryFinder();
    }
}
```

@Conditional(MemoryCondition.class)에 따라 MemoryCondition의 matches() 를 실행해보고 그 결과가 true 이면 MemoryConfig 는 정상동작한다.

따라서 memoryController, memoryFinder 가 빈으로 등록된다. MemoryCondition의 실행결과가 false 이면 MemoryConfig 는 무효화 된다. 그래서 memoryController, memoryFinder 빈은 등록되지 않는다.

### 3. 다양한 기능

1. **`@ConditionalOnClass`**: 지정된 클래스나 클래스들이 클래스패스에 존재할 때 빈을 생성.
2. **`@ConditionalOnMissingClass`**: 지정된 클래스나 클래스들이 클래스패스에 존재하지 않을 때 빈을 생성.
3. **`@ConditionalOnBean`**: 지정된 빈이 컨텍스트에 등록되어 있을 때 빈을 생성.
4. **`@ConditionalOnMissingBean`**: 지정된 빈이 컨텍스트에 등록되어 있지 않을 때 빈을 생성.
5. **`@ConditionalOnProperty`**: 지정된 프로퍼티가 설정되어 있을 때 빈을 생성.
6. **`@ConditionalOnExpression`**: 지정된 스프링 EL 표현식이 true일 때 빈을 생성.
7. 사용자 정의 조건: 직접 조건을 정의하여 **`@Conditional`**과 함께 사용할 수 있다.

```java
@Configuration
@ConditionalOnProperty(name = "myapp.feature.enabled", havingValue = "true")
public class MyFeatureConfig {
    // 이 빈은 myapp.feature.enabled=true 일 때만 생성된다.
    @Bean
    public MyFeatureBean myFeatureBean() {
        return new MyFeatureBean();
    }
}
```

위의 예제에서 **`myapp.feature.enabled`** 프로퍼티 값이 "true"로 설정되어 있을 때만 **`MyFeatureBean`**이 생성된다.

### 4. 결론

**`@Conditional`** 어노테이션은 Spring Boot와 함께 주로 사용되며, 애플리케이션의 구성을 더욱 유연하게 관리할 수 있게 해준다.

이를 이용해 필요한 빈만 활성화하고 불필요한 빈은 비활성화할 수 있어 메모리와 자원을 효율적으로 관리할 수 있다.

### 참고

- [https://www.inflearn.com/course/스프링부트-핵심원리-활용/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)
- https://lordofkangs.tistory.com/315
