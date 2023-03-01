# 해시충돌 발생시 JAVA에서는 어떻게 처리할까?

### 목차

1. [HashMap이란?](#1-hashmap이란)
2. [해시충돌이란?](#2-해시충돌이란)
3. [해시충돌이 왜 발생하는가?](#3-해시충돌이-왜-발생하는가)
4. [해시 충돌 해결 방법](#4-해시-충돌-해결-방법)
5. [Java에서는 해시 충돌을 어떻게 해결했을까?](#5-java에서는-해시-충돌을-어떻게-해결했을까)

## 1. HashMap이란?

### 정의

- Key - value 형태로 데이터를 저장.
- 하나의 Key는 하나의 value에 맵핑됨.
- Key는 고유해야만 한다.

### 내부구현

- 배열(Array)을 사용하여 구현.
- 배열에 저장하기 위해 key의 hash를 구해야함.
- hash function으로 key의 hash를 구함.
- 구해진 hash를 배열사이즈로 modular(%) 란 값을 index로 사용

```java
// hf : hash function
// M : hash_map size
array[hf(key) % M] = value
// hf(key) -> hash
// hf(key) % M -> index
```

## 2. 해시충돌이란?

### 1. 서로 다른 key들이 같은 hash를 가진 충돌

- key1과 key2이 서로 다른 key값이지만 같은 hashfunction에 넣었더니 나오는 값이 같게 될 경우.
    - 즉, key1 != key2 상태이지만 **hf(key1) == hf(key2)** 일 경우.

### 2. Hashtable에서의 해시 충돌

- hf(key1) != hf(key2) 상태 이지만 hashMap의 사이즈내로 매핑을 시키기 위해 모듈러 연산을 할 경우.
    - 즉, hf(key1) != hf(key2) 상태이지만 **hf(key1) % M == hf(key2) % M** 일 경우.

## 3. 해시충돌이 왜 발생하는가?

- perfect hash function 구현의 어려움
    - 2.1의 해시충돌과는 달리 hf(key1) != hf(key2)이 항상 보장이 되어야 하나 프로그래밍 레벨에서는 구현이 굉장히 어렵다. 왜냐하면 hash funtion의 특성상 input의 크기가 무궁무진하기 때문이다.
- key의 사이즈에 비해 hash table의 사이즈가 작기 때문

## 4. 해시 충돌 해결 방법

1. Separata chaining : 추가적인 공간을 활용하여 해결하는 방식
    - Linked list 사용
2. Open addressing : 충돌 발생시 인접한 비어 있는 공간(bucket)에 저장
    1. Linear probing : 고정폭으로 이동하여 빈 공간을 찾음
    2. Quadratic probing : 제곱수로 이동하여 빈 공간을 찾음
    3. Double Hashing : 또 다른 hash function을 사용하여 빈 공간을 찾음

### 1. Separate chaining

- 아래 그림과 같이 해쉬테이블을 살펴보자.

![image](https://user-images.githubusercontent.com/59176149/222163048-9a1bbff3-efbc-4644-8261-88abb58ea719.png)

- 1번과 같이, 100을 저장하는 상황이라면, 모듈러 연산을 통해 나온 인덱스인 1을 이용해 해쉬테이블을 접근합니다.
- 2번과 같이 100을 가진 node를 만들고 1번 인덱스가 해당 노드의 head를 가르키도록 해준다.
- 위 순서를 반복한다. 그러나 5번 상황과 같이 해당 해쉬테이블 인덱스에 이미 가르키는 노드가 있다면 마지막으로 들어간 506을 가진 노드가 hash table에 head를 가르키고 506의 노드는 이전에 저장된 100의 노드인 head를 가르키도록 해줍니다.
- 특징
    - 추가적인 메모리 공간을 사용.
    - LinkedList가 길어지면 길어질수록 탐색하는데 오랜시간이 걸릴수 있다.

### 2. Open addressing

1. Linear probing

![image](https://user-images.githubusercontent.com/59176149/222163166-6dd30c33-96f2-42c7-908a-6c2ff755c25c.png)

- 1번과 같이 해쉬테이블에 비어 있으면 1번 인덱스에 그대로 값을 저장해주면 된다.
- 2번도 마찬가지다.
- 3번의 경우, 1번 인덱스에 값이 비어있지 않다면 다음 인덱스를 가서 비어 있는지 체크를 한다. 그래서 비어있지 않은 3번 인덱스까지 내려가 값을 넣어주는걸 볼 수 있다.
- 특징
    - 비어 있는 상태가 아니라면 계속해서 인덱스를 증가시켜 비어있는지 체크를 해줘야 한다.
2. Quadratic probing

![image](https://user-images.githubusercontent.com/59176149/222163499-77e54f1b-e441-480c-9dd5-256e40cc4655.png)


- 1, 2번은 앞서 본 Linear probing와 동일하다
- 3번에서 충돌이 발생하는데 해당 값이 충돌이 발생하면 충돌횟수를 하나씩 증가 시켜 i^2만큼 모듈러 계산을 해준다.
- 즉, hf(key, i) = hf(key) + i^2 이며 3번에서 충돌이 발생하면서 i의 값을 증가 시켜 계산해준다.
- 위의 그림에서는 충돌이 2번 일어나 i = 2까지 계산되어 0에 저장된걸 볼 수 있다.
- 특징
    - Linear probing 달리 값이 몰려있는(군집) 현상을 빨리 탈출 하기위해 제곱수를 사용한다
3. Double Hashing
- hf(key, i) = hf(key) + i * 2nd_hf(key),  when 충돌횟수 i = 0, 1, 2…
- 특징
    - Linear probing과 Quadratic probing 방식처럼 값이 몰려있는 (군집) 현상이 발생하게 된다.
    - 그러므로 전혀 다른 hash funtion을 사용해서 hash값을 구해 군집현상을 줄일려고 하는 특징이 있다.
    - 위 개념으로 2nd hash function을 쓴다.

## 5. Java에서는 해시 충돌을 어떻게 해결했을까?

- jdk7까지는 linked list를 사용한 separate chaning을 활용.
- jdk8에서 linked list와 red black tree를 혼용한 separate chaining을 활용.
    - 충돌을 한 key-value쌍이 적을때는 linked list로 작동을 한다.
    - 충돌을 한 key-value쌍이 특정 임계치에 도달하면 red black tree로 작동을 한다.
    
    → 링크드 리스트는 탐색하는데 시간복잡도가 O(n)의 비용이 드나 red black tree는 O(log n) 이 들기 때문에 jdk8 에서는 성능적으로 개선이 되었다고 보면 될 것 같다.
    

### 참고

- [https://www.baeldung.com/java-hashmap-advanced](https://www.baeldung.com/java-hashmap-advanced)
- [https://www.youtube.com/watch?v=Rpbj6jMYKag&t=17s](https://www.youtube.com/watch?v=Rpbj6jMYKag&t=17s)
- [https://www.youtube.com/watch?v=tEOkPaZXGOk](https://www.youtube.com/watch?v=tEOkPaZXGOk)

