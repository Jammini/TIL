# Scale out시 세션불일치 방지를 위한 Redis 선정과정

### 목차

1. [개요](#1-개요)
2. [Redis](#2-redis)
    
    2-1. [특징](#2-1-특징)
    
3. [Memcached](#3-memcached)
    
    3-1. [특징](#3-1-특징)
    
4. [그래서 차이점이 뭐야?](#4-그래서-차이점이-뭐야)
5. [결론](#5-결론)

### 1. 개요

실제 업무 상황에서 발생 할 수 있는 상황을 만들고자, Scale out에서 세션불일치 방지하려고 한다. 그 과정에서 Redis와 Memcached 같이 메모리 기반 저장소를 비교하고 프로젝트 상황에 맞는 저장소를 선정해보자.

### 2. Redis

Redis(**Re** mote **Di** ctionary **Se** rver)는 Key-Value 쌍의 해시 맵과 같은 구조를 가진 비관계형 DBMS이다.

오픈 소스 기반으로 인메모리 데이터 구조 저장소로 메모리에 데이터를 저장한다.

따라서, 별도의 쿼리문이 필요 하지 않고, 인메모리에 저장되기 때문에 상당히 빠른 속도로 처리 할 수 있다.

### 2-1. 특징

1. 성능
    - 메모리에 저장되어 대기 시간을 낮추고 처리량을 높인다.
    - 평균적으로 읽기 및 쓰기의 작업 속도가 1ms로 디스크 기반 DB보다 빠르다
2. 유연한 데이터 구조
    - String, List, Set, Hash, Sorted Set, JSON 등 다양한 데이터 타입을 지원한다.
3. 개발 용이성
    - 쿼리문이 필요하지 않으며, 단순한 명령 구조로 데이터의 저장 조회등이 가능하다.
4. 영속성
    - 데이터를 디스크에 저장할 수 있다. 서버에 치명적인 문제가 발생하더라도 디스크에 저장된 데이터를 통해 복구가 가능하다.
5. 싱글 스레드 방식
    - 한 번에 하나의 명령어만 처리한다. 따라서 Race Condition이 거의 발생하지 않는다.
    - 단점으로는 멀티스레드를 지원하지 않기 때문에 시간복잡도가 O(n)인 명령어의 사용은 주의해서 사용해야 한다.

### 3. Memcached

Memcached는 Key-Value 쌍의 해시 맵과 같은 구조를 가진 비관계형 DBMS이다.

오픈 소스 기반으로 인메모리 데이터 구조 저장소로 메모리에 데이터를 제정한다.

따라서, 별도의 쿼리문이 필요 하지 않고, 인메모리에 저장되기 때문에 상당히 빠른 속도로 처리 할 수 있다.

### 3-1. 특징

1. 성능
    - 메모리에 저장되어 대기 시간을 낮추고 처리량을 높인다.
    - 평균적으로 읽기 및 쓰기의 작업 속도가 1ms로 디스크 기반 DB보다 빠르다
2. 분산 및 다중스레드 아키텍처
    - 서버 간 데이터를 분산 저장하여 확장성을 제공하며, 서버가 추가되면 쉽게 확장할 수 있다.
3. 영속성 X
    - 메모리 내 데이터만 보관되며, 재시작하면 모든 데이터가 손실된다

### 4. 그래서 차이점이 뭐야?

Redis와 Memcached는 둘 다 굉장히 유사하다고 생각했을것이다.

맞다. Redis와 Memcached는 메모리 내 키-값 데이터 저장소로, 오픈 소스 인메모리 DB이다.

둘 다 사용하기 쉽고 고성능을 제공하지만 고려해야 할 사항이 있다. 

아래 AWS에서 비교해준 표를 살펴보자.

<img width="706" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/af331267-44e7-4bad-92ba-ecb78d2a74c7">

<img width="706" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/fcf6d580-b94a-47b7-ae8a-8d63740de8bc">


해당 표애서 볼 수 있듯이 Mecached의 경우 Write 연산에 있어서, Redis보다 좋은 성능을 보임을 알 수 있다.

하지만, Read연산에 있어서는 Redis가 더 좋은 성능을 보인다.

이를 비교해보았을 때, Redis가 세션을 저장하는데 더 적합하다는 점을 알 수 있다.

세션 관련 작업의 경우, 쓰기 연산보다는 읽기 연산이 압도적으로 높은 작업이기 때문이다.

표에 아래 그래프를 보면 Redis가 Read/Write에 있어서 메모리를 더 효율적으로 사용하는 것을 볼 수 있다.

### 5. 결론

- 위와 같은 이유를 종합한 결과, 쓰기 성능에서는 Memcached가 앞서지만 메모리 사용효율과 처리속도, 그리고 이외에 다양한 자료구조 지원과 아키텍쳐 지원을 모두 고려한다면 Redis가 상대적으로 더 나은 선택임을 알 수 있다.

### 참고

- https://www.linkedin.com/pulse/memcached-vs-redis-which-one-pick-ranjeet-vimal/
- https://ittrue.tistory.com/317
- https://aws.amazon.com/ko/memcached/
- https://aws.amazon.com/ko/elasticache/redis-vs-memcached/
- [https://inpa.tistory.com/entry/REDIS-📚-개념-소개-사용처-캐시-세션-한눈에-쏙-정리?category=918728#redis-활용하기---캐시cache](https://inpa.tistory.com/entry/REDIS-%F0%9F%93%9A-%EA%B0%9C%EB%85%90-%EC%86%8C%EA%B0%9C-%EC%82%AC%EC%9A%A9%EC%B2%98-%EC%BA%90%EC%8B%9C-%EC%84%B8%EC%85%98-%ED%95%9C%EB%88%88%EC%97%90-%EC%8F%99-%EC%A0%95%EB%A6%AC?category=918728#redis-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0---%EC%BA%90%EC%8B%9Ccache)