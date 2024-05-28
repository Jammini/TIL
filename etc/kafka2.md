# 카프카(kafka) 조금 아는척하기 - 2

### 목차

1. [개요](#1-개요)
2. [프로듀서란?](#2-프로듀서란)
3. [프로듀서의 기본 흐름](#3-프로듀서의-기본-흐름)
4. [Sender의 기본 동작](#4-sender의-기본-동작)
5. [처리량 관련 주요 속성](#5-처리량-관련-주요-속성)
6. [전송 결과 확인 안함](#6-전송-결과-확인-안함)
7. [전송 결과 확인함](#7-전송-결과-확인함)
8. [전송 보장과 acks](#8-전송-보장과-acks)
9. [에러 유형](#9-에러-유형)
10. [실패 대응](#10-실패-대응)
11. [재시도와 메시지 중복 전송 가능성](#11-재시도와-메시지-중복-전송-가능성)
12. [재시도와 순서](#12-재시도와-순서)
13. [결론](#13-결론)

## 1. 개요

지난 카프카 아는척하기 - 1 편에서는 기본적인 구조에 대해서 정리를 하였다. 링크는 아래와 같다. https://github.com/Jammini/TIL/blob/master/etc/kafka1.md 

이번에는 프로듀서에 대해 자세히 알아보려 한다.

## 2. 프로듀서란?

토픽에 메시지 전송 → 토픽, 키, 값

```java
Properties prop = new Properties();
prop.put("bootstrap.servers", "kafka01:9092,kafka01:9092,kafka01:9092");
prop.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializder");
prop.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializder");

KafkaProducer<Integer, String> producer = new KafkaProducer<>(prop);

producer.send(new ProducerRecord)<>("topicname", "key", "value"));
producer.send(new ProducerRecord)<>("topicname", "value"));

producer.close();
```

## 3. 프로듀서의 기본 흐름

1. 그림과 같이 send() 메소드를 이용해 record를 전송하면, Serializer를 통해 byte 배열로 변환한다.
2. Partitioner를 통해서 이 메세지를 어느 topic의 파티션으로 보낼지 결정하고 변환된 byte 배열 메세지를 버퍼에 저장한다.
3. 버퍼에 단순히 저장하는게 아니고 배치로 메세지를 묶어서 저장한다.
4. Sender 배치를 차례대로 가지고와서 카프카 브로커에게 전송한다.

<img width="789" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3ba09e3b-8b2c-4647-afdc-c6fd2636b445">

## 4. Sender의 기본 동작

Sender와 send는 별도의 쓰레드로 동작하기 때문에 메세지를 보내는 동안 배치가 쌓이지 않는다거나 배치가 쌓이는 동안 send가 메세지를 보내는 상황은 만들어지지 않는다.

<img width="783" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4e198f28-20d2-4103-8451-50375ee9ea5e">

## 5. 처리량 관련 주요 속성

<img width="787" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/aea61cdb-59b5-4142-bcd2-7ee4697b55bf">

`linger.ms` 설정을 기본값 0이 아닌 10 또는 100등 지정 하여 대기시간은 주게 되면 한번에 메세지를 보내는 개수가 많아지니까 전반적인 처리량이 높아지는 효과를 볼 수 있게된다.

## 6. 전송 결과 확인 안함

```java
producer.send(new ProducerRecord)<>("topicname", "value"));
```

- 위에 메세지로 전달한다면, 전송 실패를 알 수 없다.
- 실패에 대한 별도 처리가 필요없는 메세지 전송에 사용한다.

## 7. 전송 결과 확인함

### 7-1. Future 사용

```java
Future<RecordMetadata> f = producer.send(new ProducerRecord)<>("topicname", "value"));

try {
	RecordMetadata meta = f.get(); // 블로킹으로 인해 처리량 저하
} catch (ExecutionExcection ex) {

}
```

- 배치 효과 떨어짐 → 처리량 저하
- 처리량이 낮아도 되는 경우에만 사용

### 7-2. Callback 사용

```java
producer.send(new ProducerRecord)<>("topicname", "value"),
	new Callback() {
		@Override
		public void onCompletion(RecordMetadata metadata, Exception ex) {
		
		}
	}
)
```

- 처리량 저하 없음 → 블로킹 하는 방식이 아니기 때문

## 8. 전송 보장과 acks

1. acks = 0
- 서버 응답을 기다리지 않음
- 전송 보장도 zero
2. acks = 1
- 파티션의 리더에 저장되면 응답 받음
- 리더 장애시 메시지 유실 가능
3. acks = all (또는 -1)
- 모든 리플리카에 저장되면 응답 받음
    - 브로커 min.insync.replicas 설정에 따라 달라짐
    - 예를 들어, 리플리카 개수 3, acks = all, min.insync.replicas = 2 이면, 리더에 저장하고 팔로워 중 한 개에 저장하면 성공 응답을 보낸다.

<img width="536" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/35a50c74-faae-4d14-9c6f-ab25f6129274">

- 예를 들어, 리플리카 개수 3, acks = all, min.insync.replicas = 1 이면, 리더에 저장되면 성공 응답을 보낸다. acks = 1 과 동일하다고 볼 수 있다.
- 예를 들어, 리플리카 개수 3, acks = all, min.insync.replicas = 3 이면, 리더와 팔로워 2개에 모두 저장되면 성공 응답을 보낸다.
    - 팔로워 중 한 개라도 장애가 나면 리플리카 부족으로 저장에 실패한다.
    - 즉, 리플리카 개수랑 min.insync.replicas 개수는 동일하게 지정하면 안된다.

## 9. 에러 유형

1. 전송 과정에서 실패
- 전송 타임 아웃(일시적인 네트워크 오류 등)
- 리더 다운에 의한 새 리더 선출 진행 중
- 브로커 설정 메시지 크기 한도 초과
- 등등
2. 전송 전에 실패
- 직렬화 실패, 프로듀서 자체 요청 크기 제한 초과
- 프로듀서 버퍼가 차서 기다린 시간이 최대 대기 시간 초과
- 등등

## 10. 실패 대응

### 10-1. 재시도

- 재시도 가능한 에러는 재시도 처리 ex) 브로커 응답 타임 아웃, 일시적인 리더 없음 등

**재시도 위치**

- 프로듀서는 자체적으로 브로커 전송 과정에서 에러가 발생하면 재시도 가능한 에러에 대해 재전송 시도
    - retries 속성
- send() 메서드에서 익셉션 발생시 익셉션 타입에 따라 send() 재호출
- 콜백 메서도에서 익셉션 받으면 타입에 따라 send() 재호출

**아주 아주 특별한 이유가 없다면 무한 재시도 X**

### 10-2. 기록

**추후 처리 위해 기록**

- 별도 파일, DB 등을 이용해서 실패한 메시지 기록
- 추후에 수동(또는 자동) 보정 작업 진행

**기록 위치**

- send() 메서드에서 익셉션 발생시
- send() 메서드에 전달한 콜백에서 익셉션 받는 경우
- send() 메서드가 리턴한 Future의 get() 메서드에서 익셉션 발생시

## 11. 재시도와 메시지 중복 전송 가능성

- 브로커 응답이 늦게 와서 재시도할 경우 중복 발송 가능

<img width="684" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4c7d54b6-800a-43a2-ba36-5f66fcbf8420">

재시도를 할 때는 항상 중복 전송 가능성에 유의해야한다. 참고로 enable.idempotence 속성을 이용해 중복 가능성을 줄일 수 있다.

## 12. 재시도와 순서

- max.in.flight.requests.per.connection
    - 블록킹 없이 한 커넥션에서 전송할 수 있는 최대 전송중인 요청 개수
    - 이 값이 1보다 크면 재시도 시점에 따라 메시지 순서가 바뀔 수 있음
        - 전송 순서가 중요하면 이 값을 1로 지정

<img width="775" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a7f00aa0-5e8a-409d-9d2a-bd27d70db936">

## 13. 결론

Producer에 대해 전반적인 메세지 처리 흐름이나 처리량에 대해 알아보았다. 

처리량 관련

- batch.size
- linger.ms

전송 신뢰성

- acks = all
- min.insync.replicas = 2
- replication factor = 3

재시도 주의 사항

- 중복 전송
- 순서 바뀜

### 참고

- https://www.youtube.com/watch?v=geMtm17ofPY