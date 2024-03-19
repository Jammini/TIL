# RabbitMQ 메세지큐 적용으로 성능 개선하기

### 목차

1. [개요](#1-개요)
2. [메세지 큐(Message Queue)란?](#2-메세지-큐message-queue란)
3. [RabbitMQ 설정](#3-rabbitmq-설정)
4. [메세지 발송(Producer) 구현](#4-메세지-발송producer-구현)
5. [메세지 받는(Consumer) 구현](#5-메세지-받는consumer-구현)
6. [nGrinder 성능 테스트](#6-ngrinder-성능-테스트)
7. [결론](#7-결론)

### 1. 개요

다수의 요청을 효과적으로 관리하고 비동기적으로 처리하기 위해, 메시지큐 시스템을 도입하였다. 입찰 요청이 들어오면 큐에 저장한 후 순차적으로 처리하도록 구성하였다.

전반적인 구성과정을 살펴보고 nGrinder로 테스트를 통해 성능이 얼마나 차이나는지 비교해 보자.

### 2. 메세지 큐(Message Queue)란?

메세지 큐를 간단하게 말하면 프로세스나 서비스 간에 메시지를 전송하고 저장하는 중간자 역할자이다.

 메시지 큐를 사용하면 메시지를 보내는 쪽(프로듀서)과 받는 쪽(컨슈머)이 서로의 처리 속도나 가용 상태에 영향을 받지 않고 독립적으로 작업을 수행할 수 있다.

### 3. RabbitMQ 설정

설정 클래스를 만들어 queue, exchange, biding을 정의하였다.

```java
@Configuration
public class RabbitMQConfig {

    public static final String QUEUE_NAME = "bidQueue";
    public static final String EXCHANGE_NAME = "bidExchange";
    public static final String ROUTING_KEY = "bid";

    @Bean
    Queue queue() {
        return new Queue(QUEUE_NAME, false);
    }

    @Bean
    DirectExchange exchange() {
        return new DirectExchange(EXCHANGE_NAME);
    }

    @Bean
    Binding binding(Queue queue, DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(ROUTING_KEY);
    }
}
```

### 4. 메세지 발송(Producer) 구현

애플리케이션에서 메시지를 생성하고 큐에 전달(push)하는 로직을 구현한다.

```java
String message = objectMapper.writeValueAsString(bid);
rabbitTemplate.convertAndSend(EXCHANGE_NAME, ROUTING_KEY, message);
```

### 5. 메세지 받는(Consumer) 구현

큐에서 메시지를 받아와 처리(pull)하는 로직을 구현다. 이 과정에서 비동기 처리가 이루어진다.

```java
@RabbitListener(queues = RabbitMQConfig.QUEUE_NAME)
public void receiveMessage(String message) {
    bidMapper.save(parseMessageToBid(message));
}
```

### 6. nGrinder 성능 테스트

nGrinder를 통해 메세지큐 사용 전/후 수행된 테스트 결과를 비교하고 분석해보자.

- RabbitMQ 사용 전

<img width="764" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3f483ed7-e865-46a2-8e95-cf92a07d424e">

- RabbitMQ 사용 후

<img width="764" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/1ade5993-2755-427b-9edb-65faa25e2898">

내용을 분석하여 성능 관점에서 비교해보았을 때 아래와 같이 전체적인 성능 향상률을 보였다.

- **TPS (Transactions Per Second) :** 413.5 → 800.1 / **약 94% 증가**
- 최고 TPS :  792.0 → 1,210.5 / **약 53% 증가**
- 응답 시간 : 6,164.63 → 3,378.55 / **약 45% 감소**
- 총 실행 테스트 건수 : 23,684 → 45,480 / **약 92% 증가**

### 7. 결론

메시지 큐의 도입은 경매 입찰 시스템의 성능을 크게 향상시켰으며, 이는 처리량의 증가, 응답 시간의 단축된 것을 확인하였다.

따라서, RabbitMQ를 사용함에 따라 성능적으로 개선이 이루어졌으며 전반적인 시스템 성능과 안정성이 개선되었음을 나타낸다