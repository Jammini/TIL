# 스프링에서 Conditional 어노테이션이란?

### 목차

1. [@Conditional이란?](#1-conditional이란)

### 1. @Conditional이란?

Spring Framework에서 사용되는 어노테이션 중 하나로, 빈(Bean)의 조건부 생성 및 등록을 제어하기 위해 사용되는 어노테이션이다.

즉, 컴포넌트의 Bean등록여부에 조건을 달 수 있게하는 어노테이션이다.

스프링은 IoC컨테이너에 객체를 Bean으로 등록하여 사용하는데 @Component로 선언된 클래스는 모두 Bean으로 등록하고 여기에 조건부를 달 수 가 있다.

스프링 기반의 웹사이트에서 추가기능을 넣으려고 해보자.

<img width="603" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3e9bda6f-84f8-41d9-b31e-ba73d83a1616">

스프링은 추가기능의 설정파일을 읽고 POJO에서 Bean을 IoC컨테이너에 등록한다. 이때 설정파일을 조건부로 읽는다면 필요할 때만 IoC컨테이너에 Bean을 등록할 수 있다. 여기서 @Conditional 어노테이션을 사용한다.