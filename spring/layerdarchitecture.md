# 레이어드 아키텍처(Layerd Architecture)와 테스트

### 목차

1. [개요](#1-개요)
2. [Presentation Layer(User Interface)](#2-presentation-layeruser-interface)
3. [Business Layer(Business Logic)](#3-business-layerbusiness-logic)
4. [Persstence Layer(Data Access)](#4-persstence-layerdata-access)
5. [레이어 아키텍처의 핵심요소](#5-레이어-아키텍처의-핵심요소)

### 1. 개요

스프링 MVC 기반에서 가장 많이 사용되는 아키텍처이다. 아래 그림을 살펴 보자.

레이어 별로 나누어서 개발을 하기위해 나누었으나 언급하는 곳마다 용어가 조금씩 다르기도 하고 어떤 관점에서 보는 것에 따라 달라지기도 한다.

왜 레이어를 나누는가를 중점으로 볼때 나의 답은 아래와 같다.

**관심사의 분리 때문에 레이어를 분리하는 것이라 생각한다. 관심사를 나누어서 각 계층마다 책임을 나누고 유지보수 용이하도록 하는 것이다.**

![image](https://github.com/Jammini/TIL/assets/59176149/e3fee6f9-9c7a-4994-bdfa-26fdb2e75dec)

### **2. Presentation Layer(User Interface)**

Presentation layer는 해당 시스템을 사용하는 사용자 혹은 클라이언트 시스템과 직접적으로 연결되는 부분이다. 웹사이트에서는 UI 부분에 해당하고 백엔드 API에서는 엔드포인트 부분에 해당한다. 그러므로 presentation layer에서 API의 엔드포인트들을 정의하고 전송된 HTTP 요청(request)를 읽어 들이는 로직을 구현한다. 하지만 그 이상의 역할은 담당하지 않는다. 실제 시스템이 구현하는 비즈니스 로직은 다음 레이어로 넘기게 된다.

주로 아래와 같은 역할을 담당한다.

- EndPoint
- Authentication
- JSON Translation

### 3. Business Layer(Business Logic)

Business layer라는 이름 그대로 비즈니스 로직을 구현하는 부분이다. 실제 시스템이 구현해야하는 로직을 이 레이어에서 구현하게 된다. 백엔드 API에서는  Presentation layer에서 전송된 요청을 읽어들여 요청에 맞게 동작하는 로직을 구현하면 된다. 예를 들어 회원가입 요청 시 필수적인 요소들이 다 포함되어 있지 않으면 거부한다던가 하는 로직 등이 비즈니스 로직이며, buisiness layer에서 구현하게 된다.

주로 아래와 같은 역할을 담당한다.

- Business Logic
- Validation
- Authorisation

### 4. Persstence Layer(Data Access)

Persistence layer는 데이터베이스와 관련된 로직을 구현하는 부분이다. Business layer에서 필요한 데이터를 생성, 수정, 읽기 등을 처리하여 실제로 데이터베이스에서 데이터를 저장, 수정, 읽어 들이기를 하는 역할을 한다.

주로 아래와 같은 역할을 담당한다.

- Storage Logic

### 5. 레이어 아키텍처의 핵심요소

![image](https://github.com/Jammini/TIL/assets/59176149/cc0f4f66-0f6e-44b5-99b7-fcd3dccce1dd)

레이어드 아키텍처의 핵심 요소는 **단방향 의존성**이다.

각각의 레이어는 오직 자기보다 하위에 있는 레이어에만 의존한다. 그러므로 presenstation layer는 business layer에게 의존하고, business layer는 persistence layer에게만 의존하게 된다. 또 다른 핵심 요소는 "sepration of concerns"이다. 즉, 각 레이어의 역할이 명확하다는 의미이다. 

레이어드 아키텍처의 구조로 코드를 구현하면 아래와 같은 장점이 생긴다.

1. 각 레이어가 독립적이고 역할이 분명하므로 코드의 확장성이 높아진다. 
2. 코드의 구조를 파악하기 쉬울 뿐만 아니라 재사용 가능성도 높아진다. 
3. 역할이 명확하게 나뉘어 있으므로 각 레이어를 테스트하는 테스트 코드의 작성도 훨씬 수월해진다.

### 참고

- https://kimjingo.tistory.com/159