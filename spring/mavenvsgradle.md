# Maven vs Gradle

### 목차

1. [빌드 관리 도구란?](#1-빌드-관리-도구란)
2. [Maven](#2-maven)
3. [Gradle](#3-gradle)
4. [Maven vs Gradle](#4-maven-vs-gradle)
5. [결론](#5-결론)

### 1. 빌드 관리 도구란?

- 소프트웨어 개발에서 자동화된 빌드 프로스세스를 지원하기 위해 사용되는 도구이다.
- 라이브러리 및 종속성을 관리하며 일반적으로 개발에 필요한 외부 라이브러리들을 자동으로 다운로드하고 업데이트하는 역할이다.

### 2. Maven

- Apache의 Ant 대안으로 2004년 출시.
- 빌드 중인 프로젝트, 빌드 순서, 다양한 외부 라이브러리 종속성 관계를 pom.xml에 명시
- 외부저장소에서 필요한 라이브러리와 플러그인들을 다운로드 한다음, 로컬시스템의 캐시에 모두 저장.

### 3. Gradle

- Ant와 Maven의 장점을 모아 2012년 출시.
- Android 빌드 도구로 채택 됨.
- Groovy 스크립트를 활용한 빌드 관리 도구.
- 멀티 프로젝트에 사용하기 좋음.

### 4. Maven vs Gradle

- Gradle은 Groovy와 같은 스크립트 언어로 구성할 수 있기에 XML과 달리 변수선언, if, else, for 등의 로직이 구현가능하며 간결하게 구성 가능하다.
- 아래 코드와 같이 스크립트 길이와 가독성 면에서 gradle이 좀 더 간결하다고 생각한다.
- maven의 경우 멀티 프로젝트에서 특정 설정을 다른 모듈에서 사용하려면 상속을 받아야 하지만, gradle은 설정 주입 방식을 사용하기 때문에 멀티 프로젝트에 매우 적합하다.

# pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.8</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>hello</groupId>
    <artifactId>demo-maven</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo-maven</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
 
</project>
```

# build.gradle

```
plugins {
	id 'java'
	id 'org.springframework.boot' version '2.7.8'
	id 'io.spring.dependency-management' version '1.0.15.RELEASE'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

### 5. 결론

- 구글 트렌드 지수를 보게 되면 아직 Maven이 사용률이 Gradle을 앞서고 있다.
- pom.xml과 build.gradle 코드 비교시 스크립트의 길이와 간결함의 차이가 존재하는 걸 볼 수 있다.
- gradle이 maven보다 속도면에서 우수하다고 하는 주장([https://gradle.org/gradle-vs-maven-performance/](https://gradle.org/gradle-vs-maven-performance/))
실제로 성능상 이점을 발견한 사례가 있다면 추후에 내용을 추가하도록 하자.
- 앞서 봤듯이, gradle의 성능적 주장과 프로젝트의 빌드 속도, 스크립트의 길이, 설정 주입 방식 등을 고려했을때 Gradle의 사용을 고려할 것이다.

### 참고

- [https://gradle.org/gradle-vs-maven-performance/](https://gradle.org/gradle-vs-maven-performance/)
- [https://www.interviewbit.com/blog/gradle-vs-maven/](https://www.interviewbit.com/blog/gradle-vs-maven/)
