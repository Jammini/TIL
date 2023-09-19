# spring boot의 가장 큰 장점 Auto Configuration이란? 동작 방식은?

### 목차

1. [Auto Configuration이란?](#1-auto-configuration이란)
2. [Auto Configuration 동작 방식](#2-auto-configuration-동작-방식)
    
    2-1. [main 메소드](#1-main-메소드)
    
    2-2. [SpringBootApplication](#2-springbootapplication)
    
    2-3. [EnableAutoConfiguration](#3-enableautoconfiguration)
    
    2-4. [AutoConfigurationImportSelector](#4-autoconfigurationimportselector)
    
    2-5. [동작 정리](#5-동작-정리)
    
3. [결론](#3-결론)

## 1. Auto Configuration이란?

- 스프링부트는 자동 구성(Auto Configuration) 기능을 제공하는데, 일반적으로 수많은 빈들을 자동으로 등록해주는 기능이다.
- 이러한 기능으로 인해 반복적으로 빈을 등록하고 설정하는 부분을 줄이고 편리한 개발을 할 수 있게 도와주며 스프링 부트의 핵심적인 장점이라 할 수 있다.

## 2. Auto Configuration 동작 방식

### 1. main 메소드

스프링 부트 프로젝트를 처음 생성했을때 main 메소드를 살펴보자

<img width="593" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/356a0a14-2fb8-44f0-9b51-c54d7a5c628a">

클래스에 `@SpringBootApplication`이 붙어 있는 것을 볼 수 있다. 이 어노테이션이 없다면 평상시 자바소스를 작성하는 것과 별 다르지 않을 것이다.

`run()`을  보면 `HelloSpringApplication.class`를 넘겨주며, 이 클래스를 설정 정보로 사용하겠다는 것과 같다

`@SpringBootApplication`으로 인해 톰캣이 뜨고 서버가 열리고 포트가 열리는 등 일처리를 해주는 것을 보게 된다. 즉, 해당 어노테이션은 중요한 설정정보를 가지고 있다는 것이고 이 어노테이션을 자세히 살펴보자.

### 2. SpringBootApplication

<img width="585" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3fddad55-4003-4257-aba8-a16941654d09">

빈의 등록이 크게 2가지 단계를 걸쳐 이루어지는데, 먼저 `@ComponentScan`을 통해 `@Component` 가 붙어있는 객체들을 스캔하면서 자동으로 빈을 등록해주게 된다.

이 후, `@EnableAutoConfiguration`을 통해 스프링에서 자주 사용되는 빈들을 자동적으로 컨테이너에 등록해준다.

### 3. EnableAutoConfiguration

<img width="589" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/5fc05c1e-5848-4fe8-8184-08f63658a2b6">


`@EnableAutoConfiguraion`이 AutoConfiguration을 활성화 하는 기능이 된다.

Spring Boot의 meta 파일`(spring-boot-autoconfigure/META-INF/spring.factories)`을 읽어서 미리 정의 되어있는 자바 설정 파일`(@Configuration)`들을 빈으로 등록하는 역할을 수행 한다

여기서 `@Import` 는 스프링 설정정보를 포함할때 사용하는데 `AutoConfigurationImportSelector`를 봐보자. 

### 4. AutoConfigurationImportSelector

<img width="592" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/d48e9fbb-932a-417f-b7c0-5fda582e68c8">

구현체에 있는 ImportSelector를 살펴보자.

<img width="595" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/9352807f-15d8-47f9-8abe-24af3cc38fb1">

앞서 본 스프링 설정정보를 포함할 때 `@Import`를 이용해 `ImportSelector`를 설정으로 사용할 대상을 동적으로 선택할 수 있다.

여기서 사용되는 `selectImports`를 구현체 쪽에서 사용해서 아래 경로에 있는 파일을 읽어서 스프링 부트 자동 구성으로 사용된다.

<img width="590" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/ab4f3d03-450f-400f-94d7-768ca97b54d2">


쭉 따라 가다 보면 파일을 읽어 스프링 빈으로 등록되어지고 Auto Configuration으로 설정이 된 것을 볼 수 있다.

<img width="590" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/43270a95-e6b3-4c99-ae2f-344ea73954bf">

해당 파일의 위치는 아래와 같이 경로를 탐색하여 읽어준다.

<img width="593" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/b3c95a15-08e9-459d-87ab-2b74217aeb5e">

`resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`

<img width="591" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f5c9ab17-f50d-4742-b471-5af88427545c">

가져온 구성정보를 필요한지 아닌지 판단하여 유효한 설정이라면, AutoConfiguration으로 등록이 되어지게 된다.

### 5. 동작 정리

즉, 스프링 부트 AutoConfiguration의 동작하는 방식은 이와 같다.

`@SpringBootAppliction` → `@EnableAutoConfiguration` → `@Import(AutoConfigurationImportSelector.class)` → `resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 파일을 열어서 설정 정보를 가져와 유효하다면 스프링 컨테이너에 등록되어 사용된다.

## 3. 결론

- AutoConfiguration으로 인해 개발자가 매우 편리하게 개발을 하도록 도와준다. 개발자가 직접 의존성을 설정하고 버전을 관리하는 번거로움을 줄여주며 일관된 구성 방식을 유지하는데 강력한 기능이다.
- AutoConfiguration은 라이브러리를 제공할 때 주로 사용하고, 그 외에는 사용하는 일이 거의 없긴 하다. 그 이유는 우리가 코딩 하면서 필요한 빈들은 컴포넌트스캔을 하거나 직접 등록해서 사용하면 되기 때문이다.

### 참고

- [https://docs.spring.io/spring-boot/docs/current/reference/html/auto-configuration-classes.html](https://docs.spring.io/spring-boot/docs/current/reference/html/auto-configuration-classes.html)
- [https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-auto-configuration.html](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-auto-configuration.html)
- [https://sujl95.tistory.com/58](https://sujl95.tistory.com/58)
- [https://www.inflearn.com/course/스프링부트-핵심원리-활용/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)
- [http://dveamer.github.io/backend/SpringBootAutoConfiguration.html](http://dveamer.github.io/backend/SpringBootAutoConfiguration.html)
