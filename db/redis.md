# Redis란?

### 목차

1. [Redis란?](#1-redis란)
2. [Redis의 특징](#2-redis의-특징)
3. [동시성 Race Condition 해결](#3-동시성-race-condition-해결)
4. [결론](#4-결론)

### 1. Redis란?

REDIS는 REmote DIctionary Server의 약자로 외부에서 사용 가능한 Key-Value 쌍의 해시 맵 형태의 서버라고 생각할 수 있다.

In-Memory 데이터베이스이며 다양한 데이터 구조체를 지원한다.

디스크에 데이터를 쓰는 구조가 아니라 메모리에서 데이터를 처리하기 때문에 작업 속도가 상당히 빠르다.

즉, Redis는 고성능 키-값 저장소로서 String, list, hash, set, sorted set 등의 자료 구조를 지원하는 NoSQL이다.

### 2. Redis의 특징

- 영속성을 지원하는 인 메모리 데이터 저장소
- 다양한 자료 구조를 지원함.
- 싱글 스레드 방식으로 인해 연산을 원자적으로 수행이 가능함.
- 읽기 성능 증대를 위한 서버 측 리플리케이션을 지원
- 쓰기 성능 증대를 위한 클라이언트 측 샤딩 지원
- 다양한 서비스에서 사용되며 검증된 기술

### 3. 동시성 Race Condition 해결

Redis를 이용해 Race Condition(여러개의 쓰레드가 경합해 컨텍스트 스위칭에따라 원하지 않는 결과가 발생하는 것)을 해결할 수 있게 된다. 이유는 다음과 같다.

- Redis는 기본적으로 싱글쓰레드
- Redis 자료구조는 Atomic Critical Section(동시에 여러 프로세서가 접근 하면 안되는 영역)에 대한 동기화를 제공
- 서로 다른 Transaction Read/Write를 동기화

### 4. 결론

Redis를 검색하면서 내용들을 찾아 보니 정말 여러가지 용어와 다양한 주제들이 나왔다.

어렵고 이해되는 부분이 많아 Redis가 무엇인지에 대한 간단하게만 알아보고 추가적으로 내용을 더 학습한다면 글일 이어 써도록 하자.

### 참고

- https://www.youtube.com/watch?v=Gimv7hroM8A
- https://steady-coding.tistory.com/586