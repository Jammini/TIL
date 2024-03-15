# Redis 캐시 적용으로 성능 개선하기

### 목차

1. [개요](#1-개요)
2. [캐시(Cache)란?](#2-캐시cache란)
3. [입찰 생성 시 Redis 캐시 업데이트](#3-입찰-생성-시-redis-캐시-업데이트)
4. [최고 입찰 금액 조회시 Redis 캐시 사용](#4-최고-입찰-금액-조회시-redis-캐시-사용)
5. [nGrinder 성능 테스트](#5-ngrinder-성능-테스트)
6. [결론](#6-결론)

### 1. 개요

경매에서 입찰 금액은 실시간으로 관리되고 입찰은 순간적으로 많은 요청이 발생 할 수 있다. 입찰이 많이 몰리게 되면 DB에서 입찰 금액을 빈번하게 가져와 입찰금액이 갱신하고 있는 상황이었다.

최고 입찰액을 가져오는 시간을 줄이고자 DB조회 대신 Redis에서 값을 빠르게 가져와서 응답하고자 하였고

ngrinder로 테스트를 통해 성능이 얼마나 차이나는지 비교해 보자.

### 2. 캐시(Cache)란?

캐시를 간단하게 말하면 데이터를 미리 복사해 놓는 임시 저장소를 말한다. 접근 시간이 긴 하드디스크를 읽는 대신 빠르게 접근 할 수 있도록 도와주는 역할을 한다.

### 3. 입찰 생성 시 Redis 캐시 업데이트

입찰 생성시, 해당 상품의 최고 입찰 금액을 Redis에 업데이트 한다. 이미 존재하는 최고 입찰 금액보다 높은 경우에만 Redis값을 업데이트 하였다.

```java
private void updateHighestBidInCache(Long productId, int newBidPrice) {
    String key = HIGHEST_BID_KEY + productId;
    String currentHighestBid = redisTemplate.opsForValue().get(key);

    int currentPrice = (currentHighestBid != null) ? Integer.parseInt(currentHighestBid) : 0;

    if (newBidPrice > currentPrice) {
        redisTemplate.opsForValue().set(key, String.valueOf(newBidPrice));
    }
}
```

### 4. 최고 입찰 금액 조회시 Redis 캐시 사용

최고 입찰 금액을 조회하는 DB조회 대신 Redis에서 값을 빠르게 가져온다. Redis에 값이 없는 경우에만 DB에서 조회하고, 이 값을 다시 Redis에 저장한다.

```java
public Integer findHighestPrice(Long productId) {
    String key = HIGHEST_BID_KEY + productId;
    Integ highestBid = redisTemplate.opsForValue().get(key);

    if (highestBid == null) {
        highestBid = bidMapper.selectHighestBidPriceForProduct(productId)
            .orElseGet(() -> productService.findProductById(productId).getMinBidPrice());
        redisTemplate.opsForValue().set(key, highestBid);
    }
    return highestBid;
}
```

### 5. nGrinder 성능 테스트

nGrinder를 통해 수행된 캐시 사용 전/후 수행된 테스트 결과를 비교하고 분석해보자.

- 캐시 사용전

<img width="706" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/7762ac2a-bf84-44a8-987d-725c643e3341">

- 캐시 사용후

<img width="703" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/557afa43-f1d7-44c5-a97d-9ddd752633df">


아래 주요 지표를 성능관점에서 비교해보자.

### **주요 지표 비교**

1. **TPS (Transactions Per Second)**
    - 캐시 X : 1,524.8
    - 캐시 O : 1,995.5
    
    TPS가 캐시 사용 전/후를 봤을때 상당히 향상되었다. 이는 시스템이 초당 처리할 수 있는 트랜잭션 수가 늘어난 것을 의미하며, 성능이 개선되었음을 확인할 수 있다.
    
2. **최고 TPS**
    - 캐시 X : 1,822.0
    - 캐시 O : 2,368.5
    
    최고 TPS 역시 캐시를 사용한 테스트에서 더 높게 나타났다. 이는 시스템이 피크 시에 더 많은 트랜잭션을 처리할 수 있음을 의미한다.
    
3. **평균 테스트 시간 (Response Time)**
    - 캐시 X : 1,850.14 ms
    - 캐시 O : 1,383.65 ms
    
    평균 응답 시간이 캐시를 사용한 테스트에서 더 짧아졌다. 이는 시스템이 요청을 더 빠르게 처리할 수 있게 되었음을 나타내며, 사용자 측면에서도 긍정적인 변화이다.
    

### 6. 결론

캐시를 사용함으로써 TPS와 최고 TPS가 크게 향상되었고 평균 응답 시간이 단축된 것을 확인할 수 있다.

따라서, 캐시를 사용함에 따라 성능적으로 개선이 이루어졌으며 전반적인 시스템 성능과 안정성이 개선되었음을 나타낸다.