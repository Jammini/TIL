# DTO는 어느 레이어까지 사용하는 것이 좋을까?

### 목차

1. [개요](#1-개요)
2. [[AS-IS] Controller ↔ Service 레이어간 동일한 DTO](#2-as-is-controller--service-레이어간-동일한-dto)
3. [[To-Be] Controller ↔ Service 레이어간 각각의 DTO](#3-to-be-controller--service-레이어간-각각의-dto)
4. [결론](#4-결론)

### 1. 개요

DTO(Data Transfer Object)는 레이어 계층 간 데이터를 전송하는데 사용되는 객체로 많은 사람들이 사용하고 있다. 이유는 다음과 같이 간단하다.

1. DTO를 이용해 각 계층이 필요한 데이터만 전달 받아 처리할 수 있다.
2. 시스템으 결합도를 낮추고 유지 보수성을 향상시킨다.

따라서, “DTO를 사용할 때 어느 레이어까지 사용하는 것이 좋을지”에 대해서는 프로젝트 과정에서 반드시 고려해야 할 요소라고 생각한다. 이를 통해 개발자는 보다 유연하고 확장 가능한 시스템을 구축하고 더 나은 서비스를 제공해 줄 것이다.

이번에 게시글을 작성하면서 **DTO에 대한 레이어 범위를 잡아 정리하고 왜 그렇게 기준을 잡았는지 대해**서도 작성해보려한다.

### 2. [AS-IS]  Controller ↔ Service 레이어간 동일한 DTO

![image](https://github.com/user-attachments/assets/44a641e1-aeb0-4d56-8d7c-2ea6b0d0059c)

DTO는 `@RequestBody` 를 통해  Controller Layer에 전달되고 전달된 DTO를 그대로 Service Layer에 전달 해서 비즈니스 로직을 구현했다.

게시판에 게시글을 등록하는 API 코드를 살펴보자.

![image](https://github.com/user-attachments/assets/c52f8c2e-f62b-4996-90cc-264720cf01af)

![image](https://github.com/user-attachments/assets/9b6d76ef-3be9-4d24-a2f6-1eb0ce084441)

웹단에서 보낸 JSON 형태의 데이터를 Controller Layer에서 `@RequestBody` 를 사용해 `PostCreateRequest` 로 받은 후 그 request 값을 Service Layer에 그대로 넘기는 것을 볼 수 있다.

![image](https://github.com/user-attachments/assets/70f0f780-d22f-4bd8-a909-0ba7c485a744)

Controller Layeer에서 보낸 DTO를 Service Layer에서 그대로 받아서 사용하였다.

이 방법은 가장 직관적이면서 편한 방법이다.

But, 이렇게 되면 다음과 같은 문제가 생긴다.

Controller와 Service는 이 2개의 Layer간 DTO를 통해서 의존 관계가 생기게 된다.

정확히 말하자면 Service 에서 write라는 메소드 파라미터에 Controller의 DTO를 사용하면서 의존관계가 생겨버린 다는 것이다.

나중에 예를 들어, 서비스가 커지게 되면 Controller나 Service의 모듈을 분리하고 싶을때 문제가 될 수도 있다.

기본적으로 Layered architecture는 상위레이어가 하위레이어를 몰라야 한다.

### 3. [To-Be]  Controller ↔ Service 레이어간 각각의 DTO

그래서 나는 다음과 같이 변경하여 작성하였다.

![image](https://github.com/user-attachments/assets/2b5ef1ba-5242-4b52-bf01-fdcc55a067a8)

DTO는 `@RequestBody` 를 통해  Controller Layer에 전달되고 전달된 DTO는 Service Layer로 넘겨질때 변환된 DTO를 이용해서 비즈니스 로직을 구현했다.

![image](https://github.com/user-attachments/assets/137c3875-95ef-42b3-bd9a-e47f8eb88482)

![image](https://github.com/user-attachments/assets/03952b86-5898-4fd0-ba11-701d0ed94c37)

웹단에서 보낸 JSON 형태의 데이터를 Controller Layer에서 `@RequestBody` 를 사용해 `PostCreateRequest` 로 받은 request를 Service용 DTO로 변환해서 Service Layer에 넘기는 것을 볼 수 있다.

![image](https://github.com/user-attachments/assets/dd37bf4a-a612-4eb0-a9e5-a9a32372a41e)

![image](https://github.com/user-attachments/assets/0741d280-2bcc-484c-83a5-7b6eeda2bfc3)

이렇게 Controller의 `PostCreateRequest` DTO와 Service의 `PostCreateServiceRequest` DTO를 각각 만들어서 사용하여 깔끔하게 분리를 하였다.

이 부분이 사실 귀찮고 번거로운 작업이라는 점은 분명하다. 하지만 불편함에도 이점이 확실하다고 생각했다.

만약, 규모가 커져서 Controller와 Service의 Layer의 모듈을 분리해야한다면 Service Layer에서 Controller DTO를 계속 가지고 있어서 분리할 때 번거롭게 될 수 있다.

Cotnroller DTO를 통해 validation을 체크 해주는 역할을 확실히 분리해주고 Service에서는 비즈니스 로직에 집중해서 작성할 수 있게 된다.

![image](https://github.com/user-attachments/assets/456fd0d9-27e9-45bf-a7c0-518d129bb523)

또한, Service DTO 에서는 `@NotBlank` 와 같은 검증을 Controller에서 다 해주었기 때문에 쓸필요가 없어진다.

Service의 모듈을 분리하더라도 Controller DTO를 분리했기 때문에 `spring-boot-starter-validation` 를 가져갈 필요도 사라지는 효과도 있다.

이제 `PostCreateServiceRequest` 클래스는 Clean한 POJO 형태의 DTO로 관리를 하고 `PostCreateRequest` 클래스는 Controller에서 validation의 책임을 가져갈 수 있도록 책임을 분리해준다.

만약 서비스가 확장되고 커져서 여러개의 다른 채널(자유게시판, 유머게시판, 정치게시판 등)이 생겼을 경우에 Controller 와 Service DTO가 같다면 채널마다 각각의 DTO 알아야 한다는 문제가 생길 수 있을 것이다.

즉, 내가 Service Layer에 기능을 동시에 사용하면서 Web단의 Request가 변경되어도 Service는 영향을 받지 않는게 좋은 설계라 생각한다.

### 4.  결론

DTO를 Controller와 Service 레이어 간에 분리하면 초기에는 귀찮고 번거로운 작업처럼 보일 수 있지만, 장기적으로는 더 나은 유연성과 유지보수성을 제공한다.

Controller 레이어와 Service 레이어 간의 DTO를 분리함으로써 다음과 같은 이점을 얻을 수 있을 것이다.

1. **의존성 감소**

   Service 레이어가 Controller DTO에 의존하지 않으므로, 레이어 간 결합도가 낮아지고 변경에 따른 영향을 최소화할 수 있다.

2. **책임 분리**

   Controller에서는 클라이언트 요청 데이터의 유효성을 검사하는 역할에 집중할 수 있고, Service 레이어에서는 비즈니스 로직에만 집중할 수 있다.

3. **모듈화 용이성**

   서비스가 확장되어 레이어 간 모듈을 분리해야 할 경우, Controller와 Service가 독립적인 DTO를 사용하기 때문에 분리 작업이 훨씬 용이하다.

4. **확장성 향상**

   다양한 채널(예: 자유게시판, 유머게시판 등)에서 요청이 들어오더라도 Service 레이어는 동일한 내부 DTO로 처리할 수 있으므로, 확장된 요구사항에도 안정적으로 대응할 수 있다.


즉, 초기 개발에서는 추가적인 작업이 될지만, 복잡한 시스템에서는 유지보수성과 확장성의 장점이 있다. DTO 분리를 통해 레이어 간 책임과 역할을 설정하고, 변경에 유연하게 구축할 수 있다.
