# 카프카(kafka) 조금 아는척하기 - 3

### 목차

1. [개요](#1-개요)
2. [컨슈머](#2-컨슈머)
3. [토픽 파티션은 그룹 단위 할당](#3-토픽-파티션은-그룹-단위-할당)
4. [커밋과 오프셋](#4-커밋과-오프셋)
5. [커밋된 오프셋이 없는 경우](#5-커밋된-오프셋이-없는-경우)
6. [컨슈머 설정](#6-컨슈머-설정)
7. [자동 커밋/수동 커밋](#7-자동-커밋수동-커밋)
8. [재처리와 순서](#8-재처리와-순서)
9. [세션 타임아웃, 하트비트, 최대 poll 간격](#9-세션-타임아웃-하트비트-최대-poll-간격)
10. [종료 처리](#10-종료-처리)
11. [주의: 쓰레드 안전하지 않음](#11-주의-쓰레드-안전하지-않음)
12. [결론](#12-결론)

## 1. 개요

지난번에 이어서 이번에는 컨슈머에 대해 알아보려고 한다. 이전에 1편과 2편은 다음과 같다.

1편 - https://github.com/Jammini/TIL/blob/master/etc/kafka1.md

2편 - https://github.com/Jammini/TIL/blob/master/etc/kafka2.md

## 2. 컨슈머

- 토픽 파티션에서 레코드 조회

```java
Properties prop = new Properties();
prop.put("bootstrap.servers", "localhost:9092");
prop.put("group.id", "group1");
prop.put("key.deserializer", "org.apache.kafka.common.serialization.StringDesrializer");
prop.put("value.deserializer", "org.apache.kafka.common.serialization.StringDesrializer");

KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(prop);
consumer.subscribe(Collections.singleton("simple")); // 토픽 구독
while(조건) {
	ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
	for(ConsumerRecord<String, String> record : records) {
		System.out.println(record.value() + ":" + record.topic() + ":" + record.partition() + ":" + record.offset());)
	}
}
consumer.close();
```

## 3. 토픽 파티션은 그룹 단위 할당

- 컨슈머 그룹 단위로 파티션 할당

<img width="792" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/8146255e-f503-4e2d-9db2-9ba4e08faa4b">

마지막 그림 같이 컨슈머 개수가 파티션 개수보다 커지면 안된다는 점이다.

만약 처리량이 떨어져서 컨슈머 개수를 늘릴거라면 파티션 개수도 늘려야 한다.

## 4. 커밋과 오프셋

<img width="747" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/9c1a8c1d-699f-418e-9550-705bb0042a3d">

## 5. 커밋된 오프셋이 없는 경우

- 처음 접근이거나 커밋한 오프셋이 없는 경우
- auto.offset.reset 설정 사용
    - earliest : 맨 처음 오프셋 사용
    - latest : 가장 마지막 오프셋 사용 (기본값)
    - none : 컨슈머 그룹에 대한 이전 커밋이 없으면 익셉션 발생

<img width="781" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/760ce716-244d-4fdd-a202-f455ed5e3174">


## 6. 컨슈머 설정

**조회에 영향을 주는 주요 설정**

- fetch.min.bytes: 조회시 브로커가 전송할 최소 데이터 크기
    - 기본 값 : 1
    - 이 값이 크면 대기 시간은 늘지만 처리량이 증가
- fetch.max.wait.ms: 데이터가 최소 크기가 될 때까지 기다릴 시간
    - 기본 값 : 500
    - 브로커가 리턴할 때까지 대기하는 시간으로 poll() 메서드의 대기 시간과 다름
- max.partition.fetch.bytes: 파티션 당 서버가 리턴할 수 있는 최대 크기
    - 기본 값 : 1048576 (1MB)

## 7. 자동 커밋/수동 커밋

- enable.auto.commit 설정
    - true : 일정 주기로 컨슈머가 읽은 오프셋을 커밋(기본값)
    - false : 수동으로 커밋 실행
- auto.commit.interval.ms : 자동 커밋 주기
    - 기본값 : 5000 (5초)
- poll(), close() 메서드 호출시 자동 커밋 실행

### 7-1. 수동 커밋 : 동기/비동기 커밋

```java
ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
for(ConsumerRecord<String, String> record : records) {
	// ... 처리
}
try {
	consumer.commitSync();
} catch(Exception ex) {
	// 커밋 실패시 에러 발생
}
```

```java
ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
for(ConsumerRecord<String, String> record : records) {
	// ... 처리
}
consumer.commitASync(); // commitAsync(OffsetCommitCallback callback)
```

## 8. 재처리와 순서

- 동일 메시지 조회 가능성
    - 일시적 커밋 실패, 리밸런스(새로운 컨슈머가 추가되거나 빠지거나 등) 등에 의해 발생
- 컨슈머는 멱등성(idempotence)을 고려해야 함
    - 예: 아래 메시지를 재처리 할 경우
        - 조회수 1증가 → 좋아요 1증가 → 조회수 1증가
    - 단순 처리하면 조회수는 2가 아닌 4가 될수 있음
- 데이터 특성에 따라 타임스탬프, 일련 번호 등을 활용

## 9. 세션 타임아웃, 하트비트, 최대 poll 간격

- 컨슈머는 하트비트를 전송해서 연결 유지
    - 브로커는 일정 시간 컨슈머로부터 하트비트가 없으면 컨슈머를 그룹에서 빼고 리밸런스 진행
    - 관련 설정
        - session.timeout.ms : 세션 타임 아웃 시간 (기본값 10초)
        - heartbeat.interval.ms : 하트비트 전송 주기 (기본값 3초) → session.timeout.ms의 1/3 이하 추천
- max.poll.interval.ms : poll() 메서드의 최대 호출 간격
    - 이 시간이 지나도록 poll()하지 않으면 컨슈머를 그룹에서 빼고 리밸런스 진행

## 10. 종료 처리

- 다른 쓰레드에서 wakeup() 메서드 호출
    - poll() 메서드가 WakeupException 발생 → close() 메서드로 종료 처리

```java
KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(prop);
consumer.subscribe(Collections.singleton("simple"));
try {
	while(true) {
		ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1)); // wakeup() 호출시 익셉션 발생
		// ... records 처리
		try {
			consumer.commitAsync();
		} catch(Exception e) {
			e.printStackTrace();
		}
	}
} catch(Exception e) {
	
} finally {
	consumer.close();
}
```

## 11. 주의: 쓰레드 안전하지 않음

- KafkaConsumer는 쓰레드에 안전하지 않음
    - 여러 쓰레드에서 동시에 사용하지 말 것!
    - wakeup() 메서드는 예외

## 12. 결론

- 1, 2, 3편에 대해서 간략하게 카프카에 대해서 알아보았다.
- 더 깊게 공부가 필요 하다고 판단된다면 관련서적이나 문서를 찾아보면서 공부를 할 예정이다.

### 참고

- https://www.youtube.com/watch?v=xqrIDHbGjOY