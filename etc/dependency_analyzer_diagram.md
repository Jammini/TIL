# [IntelliJ] Dependency Analyzer & Dependency Diagram 의존성 확인하기

### 목차

1. [개요](#1-개요)
2. [Dependency Analyzer](#2-dependency-analyzer)
3. [Dependency Diagram](#3-dependency-diagram)

### 1. 개요

<img width="698" alt="image" src="https://github.com/user-attachments/assets/6346bbb0-11fb-44ea-8602-5f57ed9645cb">

프로젝트를 진행하면서 코드리뷰를 달던 중에 `spring-boot-starter-web` 이 `spring-boot-starter`을 따로 추가한 코드를 보고 다음과 같이 댓글을 달았다.

댓글 답변과 같이  `spring-boot-starter-web` 의존성이  `spring-boot-starter`를 가지고 있기에  `spring-boot-starter-web` 의존성만 가지고 있어도 충분하다.

이 리뷰를 계기로 “IntelliJ내에서 디펜던시의 종속 관계를 알아보는 방법이 없을까” 라고 생각했고 알아봤던 내용을 작성해보려고 한다. IntelliJ가 의존성을 어떻게 분석하는데 도움이 될 수 있는지 살펴보자.

### 2. Dependency Analyzer

<img width="704" alt="image" src="https://github.com/user-attachments/assets/1644dac8-e899-49b5-91c2-b2d0130fcc75">

가장 간단한 방법으로는 IntelliJ 내에 오른쪽 탭에 Maven이나 Gradle 도구 창에서 dependencies를 확인할 수 있다.

<img width="656" alt="image" src="https://github.com/user-attachments/assets/3dd59fc0-c04a-4449-a99b-de50f34a46df">

### 3. Dependency Diagram

프로젝트 도구 창에서 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 다이어그램 표시를 선택해서 사용할 수 있다.

<img width="521" alt="image" src="https://github.com/user-attachments/assets/baa4951e-ac98-4835-82f8-93ed7a1b303d">

위와 같이 Show Diagram을 누르게 되면 아래와 같이 Select Diagram Type 이 뜰것이다.

팝업으로 뜨게 원한다면 Show Diagram Popup을 누르면 된다.

<img width="296" alt="image" src="https://github.com/user-attachments/assets/8421af29-16f4-4ee7-96b8-c7af34997b87">

Gradle Dependencies를 클릭한다.

<img width="698" alt="image" src="https://github.com/user-attachments/assets/6c4b876c-cd4c-4ae6-9a73-caee4df3e819">

이와 같이 만들어진 다이어그램을 보면 프로젝트가 가진 Dependencies 를 확인할 수 있고 관계되어 있는 디펜던시까지 확인할 수 있다.

```jsx
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}
```

나는 현재 프로젝트에서 기본적으로 이렇게 디펜던시를 정의했는데 다이어그램에 보다시피 내가 정의한 h2, data-jpa, lombok, web, valiadation 을 다이어그램에서 확인할 수 있다.

여기서 처음 개요에 소개 되었던  `spring-boot-starter-web` 의존성을 살펴보면 아래와 같은 의존성을 포함하고 있는 것을 확인할 수 있다.

https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web/3.4.0

<img width="599" alt="image" src="https://github.com/user-attachments/assets/863dd27d-a5fb-454d-92a4-e8725d27e12f">

But, IntelliJ에서도 아래와 같이 확인할 수 있다.

해당 다이어그램에서  `spring-boot-starter-web` 의존성을 누르면 종속된 의존성을 파악할 수 있는 것이다.

<img width="607" alt="image" src="https://github.com/user-attachments/assets/3a4d27d5-f26c-4db6-85a2-823cd5565385">

이렇게 IntelliJ에서의 디펜던시를 보는법과 다이어그램 보는법을 알아봤다.

Maven Repository를 이용해서 해당 의존성을 포함하는 것을 확인할 수 있지만 이렇게 IntelliJ를 이용해 시각적으로 다이어그램으로 표현해준다면 눈에 확 띄게 된다.

갓텔리제이 아주 굳~
