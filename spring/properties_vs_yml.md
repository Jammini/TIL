# properteis vs yml

### 목차

1. [개요](#1-개요)
2. [properties](#2-properties)
3. [yml](#3-yml)
4. [properties vs yml](#4-properties-vs-yml)
5. [결론](#5-결론)

### 1. 개요

우리는 데이터베이스와 관련된 설정이나 스프링과 관련 설정등을 할때 주로 저 두개를 많이 사용하게 된다.

최근 프로젝트나 많은 오픈 소스에서는 properties보다 yml을 많이 사용하고 있는 추세이다. 어떠한 차이점으로 인해 많은 프로젝트들이 yml을 선호하는지 알아보려고 한다.

### 2. properties

스프링부트가 애플리케이션을 구동할때 자동으로 로딩하는 파일이다.

key - value 형식으로 값을 정의하면 애플리케이션에 참조하여 사용할 수 있다.

```properties
spring.h2.console.path=/h2-console
spring.datasource.url=jdbc:h2:~/test
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password

# 리스트
my.servers[0]=dev.example.com
my.servers[1]=another.example.com
```

### 3. yml

아래 코드는 YAML(**Y**et **A**nother **M**arkup **L**anguage)의 문법이다.

예전 스프링 같은 경우 XML을 이용해 설정을 많이 했는데 여기서도 M은 Markup 이라는 의미를 가진다.

요즘에는 **Y**AML **A**in’t **M**arkup **L**anguage (마크업 언어가 아니다) 라는 의미로도 사용되고 있다. 즉, YAML이 문서 마크업을 하는게 하니라 데이터 그 자체라는 것을 의미하려고 이런 문장도 나온 것 같다.

결론적으로, **신세대 요즘 잘 사용하는 마크업 언어**라고 생각하면 될 것 같다.

```yaml
spring:
    h2:
	    console:
            path: /h2-console
    datasource:
        url: jdbc:h2:~/test
        driverClassName: org.h2.Driver
        username: sa
        password: password
      
# 리스트
my:
  servers: [dev.example.com, another.example.com]
my:
  servers:
    - dev.example.com
    - another.example.com
```

properties와 다르게 계층 구조로 표현할 수 있어서 들여쓰기를 통해 구분하게 되며 prefix의 중복 제거가 가능해지고 가독성이 좋다.

### 4. properties vs yml

|  | .properties | YAML (.yml) |
| --- | --- | --- |
| 명세서 존재 | X.(가장 유사한 문서로는 javadoc) | O.(https://yaml.org/spec/) |
| 어느 언어에서 사용되는가? | 주로 Java | Python, Ruby, Java 등 |
| 계층 구조 | 비계층적 | 계층적 |
| Spring - @PropertySource 지원 | O | X |
| profile 여러개 사용 | 파일 분리 필요
Spring Boot 2.4부터 구분 가능 | 한 파일에서 구분 가능 |

### 5. 결론

어떤 것을 사용해도 정답이 있는건 아니지만 properties 방식이 중복이 많고 가독성이 좋지 않아 yml을 많이 선호하는 추세로 생각된다.

### 참고

- https://www.baeldung.com/spring-yaml-vs-properties
- https://yeonyeon.tistory.com/245
