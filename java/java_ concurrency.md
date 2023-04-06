# 자바에서 동시성 문제를 해결하기 위한 방법

### 목차

1. [개요](#1-개요)
2. [synchronized](#2-synchronized)
    
    2-1. [synchronized 블럭](#1-synchronized-블럭)
    
    2-2. [synchronized 함수](#2-synchronized-함수)
    
3. [volatile](#3-volatile)
4. [atomic](#4-atomic)
    
    4-1. [CAS(Compare And Swap) 알고리즘](#1-cascompare-and-swap-알고리즘)
    
5. [결론](#5-결론)

## 1. 개요

- 자바에서 동시성 문제를 해결하기 위한 키워드로 synchronized, volatile, atomic에 대해 정리해보려고 한다.
- 멀티쓰레드 환경에서 중요하게 다루어야 할 가시성과 원자성을 위 키워드는 어떻게 확보하였는지도 살펴보자.

## 2. synchronized

- 한 스레드가 객체의 synchronized 메서드 또는 synchronized 블럭을 실행하는 경우, 다른 스레드는 해당 객체의 다른 synchronized 메서드나 블럭을 실행할 수 없음을 보장해준다.
- synchronized의 경우 블럭에 진입하기 전에 CPU 캐시와 메인 메모리 값을 동기화하여 가시성을 해결한다.

### 1. s**ynchronized 블럭**

```java
synchronized(Lock 객체) {
	//임계 영역 (Thread 동시접근이 불가능)
}
```

- 즉, 같은 Lock객체를 사용하는 여러 synchronized 블럭은 한 쓰레드만 진입해 있더라고 다른 쓰레드는 해당 lock을 얻을때까지 아무 작업을 하지 못한채 기다려야 한다.

### 2. s**ynchronized 함수**

```java
public synchronized void method() {
	// 자원 경합 (race condition)이 일어나는 코드
}
```

- 함수 통채로 임계영역을 구성하는 방법이다.
- 위의 Synchronized 블럭과 동일하다고 보면 된다. this가 생략되었다고 생각하면 된다. this를 사용하는 블럭의 lock이 공유된 것이며 하나만 쓰레드를 점유하고 있으면 다른 쓰레드는 접근할 수 없다.

## 3. volatile

- 변수를 volatile로 선언하면, 해당 변수를 읽거나 쓸 때 항상 메인 메모리에서 읽거나 쓴다.

![image](https://user-images.githubusercontent.com/59176149/230401913-fde779dc-ec7b-4aed-8ed4-fea8e71be5e3.png)


- Ex)
    1. Thread 1이 메인 메모리내 counter 값인 0을 읽어 1을 더하는 연산을 진행한다
    2. Thread 1이 연산은 했지만 메인 메모리에 값을 반영하기 전에 Thread2가 메인 메모리의 counter 값인 0을 읽어와서 1을 더하는 연산을 진행한다.
    3. 결과적으로 최종결과가 2가 되어야 하는 상황이지만 1이 되는 상황이 되버린다.
- CPU는 연산을 수행하기 위에 읽고(READ) 연산하고(MODIFY) 쓰는(WRITE) 작업이 이루어지는데 연산하고 쓰기직전에 다른 쓰레드가 값을 읽는다면 문제가 발생되며 예상치 못한 결과가 나올 수 있는 것이다.
- 이처럼 volatile이 가시성에 대해서 보장을 해주며 단일쓰레드 일때는 문제가 발생하지 않겠지만, 멀티쓰레드 환경에서 위 그림과 같은 상황처럼 CPU Cache에 저장된 값이 다르기에 변수 값이 불일치되며 원자성을 보장해야 하는 문제가 발생한다.

## 4. atomic

- Synchronized와 같이 블로킹 동기화는 여러가지 단점이 존재하는데 가장 큰 단점이 성능이슈다.
- 어떤 쓰레드는 Lock을 확보하려고 경쟁하거나 또 다른 쓰레드는 Lock을 확보하지 못해 Blocking 상태와 같은 이런 문제가 성능문제로 이어진다.
- 위와 같은 문제를 해결하기 위해 Atomic Hardware Primitives를 제공한다.
- Atomic은 CAS 알고리즘을 이용하여 원자성 뿐만 아니라 가시성 문제도 해결해 준다. 그리고 non-blocking방식을 취하므로 blocking방식인 synchronized보다 성능상 우수하다.

### 1. **CAS(Compare And Swap) 알고리즘**

![image](https://user-images.githubusercontent.com/59176149/230401913-fde779dc-ec7b-4aed-8ed4-fea8e71be5e3.png)


- Ex)
    1. Thread 1과 Thread 2는 메인메모리에 있는 counter 변수를 읽어 CPU Cache에 저장한다.
    2. 각 쓰레드는 counter 값을 연산한다.
    3. Thread 1과 Thread 2는 연산하고 난 counter 값을 메인메모리에 반영하기 전의 counter값과 메인메모리에 저장된 counter값을 비교한다.
        1. 기존 값으로 던진 값이 메인 메모리가 가지고있는 값과 같다면 변경할 값을 반영해준다. 반환값으로 true를 리턴한다.
        2. 반대로 기존 값으로 던진 값이 메인 메모리가 가지고있는 값과 다르다면 값을 반영하지 않고 false를 리턴하며 메인 메모리에 저장된 값을 읽어 2번으로 돌아간다.

## 5. 결론

- 여러 쓰레드가 접근 가능하도록 공유 변수에 대한 가시성과 원자성을 확보해야한다.
- synchronized
    - 한 스레드가 객체의 synchronized 메서드 또는 synchronized 블럭을 실행하는 경우, 다른 스레드는 해당 객체의 다른 synchronized 메서드나 블럭을 실행할 수 없음을 보장한다. 블럭킹 상태는 결국 성능 문제로 이어질 수 있다.
- volatile
    - 변수를 volatile로 선언하면, 해당 변수를 읽거나 쓸 때 항상 메인 메모리에서 읽거나 쓴다. 가시성을 보장해주며 단일쓰레드 환경에서는 문제가 발생하지 않겠지만, 멀티쓰레드 환경에서는 CPU Cache에 값과 메인메모리 값이 다른 쓰레드 연산으로 인해 원자성을 보장할 수 없는 문제가 발생한다.
- atomic
    - CAS(Compared And Swap)이라는 알고리즘으로 원자성을 보장한다. CPU 캐시메모리와 메모리를 비교하여 일치한다면 적용하고 일치하지 않으면 다시 시도를 하여 원자성을 보장시킨다.

### 참고

- [https://velog.io/@syleemk/Java-Concurrent-Programming-가시성과-원자성](https://velog.io/@syleemk/Java-Concurrent-Programming-%EA%B0%80%EC%8B%9C%EC%84%B1%EA%B3%BC-%EC%9B%90%EC%9E%90%EC%84%B1)
- [https://steady-coding.tistory.com/568](https://steady-coding.tistory.com/568)
