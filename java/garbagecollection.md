# GC(GarbageCollection)는 어떻게 동작하며 왜 필요할까?

### 목차

1. [GC는 무엇인가?](#1-gc는-무엇인가)
2. [GC는 왜 필요할까?](#2-gc는-왜-필요할까)
3. [GC 알고리즘](#3-gc-알고리즘)
    
    3-1. [Reference Counting](#1-reference-counting)
    
    3-2. [Mark And Sweep](#2-mark-and-sweep)
    
4. [GC는 어떻게 동작하는가?](#4-gc는-어떻게-동작하는가)
    
    4-1. [동작구조](#1-동작구조)
    
    4-2. [Minor GC랑 Major GC는 무슨 차이지?](#2-minor-gc랑-major-gc는-무슨-차이지)
    
    4-3. [young영역과 old영역 왜 나눠놨지?](#3-young영역과-old영역-왜-나눠놨지)
    
5. [결론](#5-결론)

## 1. GC는 무엇인가?

- 위키백과를 보면 다음과 같이 나와있다.

<img width="646" alt="image" src="https://user-images.githubusercontent.com/59176149/233098759-fad0eed1-4b12-4095-b8a9-9cb0b1eb688a.png">

- 즉, 동적 할당된 메모리 영역 가운데 더 이상 사용할 수 없게 된 영역을 탐지하여 자동으로 해제해준다.

## 2. GC는 왜 필요할까?

- GC를 사용하면 수동으로 메모리 관리를 했던 여러 작업이나 에러들을 방지할 수 있다.

장점

1. 메모리 누수를 막을 수 있다
2. 해제된 메모리에 접근을 막을 수 있다.
3. 해제한 메모리 또 해제하는 경우를 막을 수 있다

단점

1. GC 작업은 순수 오버헤드
2. 개발자는 언제 GC가 메모리를 해제하는지 모른다.

## 3. GC 알고리즘

GC 알고리즘을 보기 전 ROOT SPACE는 아래 그림에 노란색 박스를 가르킨다고 생각하자

<img width="645" alt="image" src="https://user-images.githubusercontent.com/59176149/233098925-6cae98ce-eeb2-4a33-9a6f-b6b907c112ae.png">

### 1. Reference Counting

<img width="637" alt="image" src="https://user-images.githubusercontent.com/59176149/233099193-8b7d5873-57f7-422b-8058-a53a4987221e.png">

객체들은 본인에게 접근할 수 있는 수를 나타내는 reference count 의미의 숫자를 가지고 있다.

reference count가 0이 되면 해당 객체에 접근 중인 객체가 없다는 의미로 GC의 대상이 된다.

그렇기에 별도로 GC를 통해 삭제될 객체들을  찾는 일을 하지 않아도 되지만 큰 단점이 존재한다.

<img width="642" alt="image" src="https://user-images.githubusercontent.com/59176149/233099257-77299148-7126-49ad-aa9e-8fdb86e318d1.png">

이처럼 GC ROOTS 영역에서 참조 중이지 않고 Heap Space 객체들끼리 **순환참조** 중일 경우, GC의 대상이 되지 않기 때문에 **Memory Leak이 발생**하게 된다.

### 2. Mark And Sweep

위에서 설명한 Reference Counting 의 순환참조 문제를 해결하는 알고리즘이다

JVM은 GC는 기본적으로 이 알고리즘으로 동작한다.

Root에서부터 해당 객체에 접근 가능한지를 해제의 기준으로 삼기에 아래와 같은 특징이 존재한다.

- 의도적으로 GC를 실행시켜야 한다.
- 애플리케이션 실행과 GC실행이 병행된다.

### - Mark

1. GC ROOTS로 부터 모든 변수들을 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹을 한다.
2. Marking되지 않은 객체는 사용이 안 되고 있는 객체이기 때문에 GC의 대상이 된다.

<img width="650" alt="image" src="https://user-images.githubusercontent.com/59176149/233099344-2809a882-12ff-4266-8266-658d4aed0918.png">

### - Sweep

1. 마킹되지 않은 객체들을 Heap 영역에서 제거한다.

이때, 루트로 부터 연결된 객체들은 Reachable, 그렇지 않은 경우를 Unreachable이다.

이 후 Mark And Sweep을 사용하는 방식 중에서 Compact 라고 하는 과정이 추가되는 경우도 존재한다.

### - Compact

4. Sweep 후에 분산 된 객체들을 Heap의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분으로 나눈다.

- Compact 과정을 추가하는 이유는 무엇일까?

Sweep 과정에서 메모리를 정리하다보면 **메모리 단편화(Memory Fragmentation)**가 발생할 수 밖에 없기 때문이다.

그 말은 메모리 영역 중간중간 비어있는 곳들이 존재하고, 채워져있는 곳들이 존재하게 되는 것이다.

이렇게 된다면 다음과 같은 문제들이 발생하게 됩니다.

1. 새로운 객체를 할당할 적절한 여유 공간을 가지는 메모리 블록을 찾기 위해서 시간을 더 소모하게 된다.
2. 객체를 새로 생성할 때는 그 객체가 점유할 메모리 공간은 반드시 연속적이어야 한다.
3. 그렇기에 전체 가용 메모리 공간은 여유롭지만 연속된 공간이 부족하여 객체 생성에 실패할 수 있게 된다.

<img width="656" alt="image" src="https://user-images.githubusercontent.com/59176149/233099633-d27c6776-eac2-4da5-8460-67e69e471c3d.png">

## 4. GC는 어떻게 동작하는가?

어떻게 동작하는지에 대해서는 JVM 구조가 선행되어야 하므로 해당 내용을 자세히 알고 싶다면 [이 링크](https://github.com/Jammini/TIL/blob/master/java/%EB%8D%94-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%A1%B0%EC%9E%91%ED%95%98%EB%8A%94-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%B0%A9%EB%B2%95.md)로 이동하여 알아보자.

즉, JVM에서 GC가 동작하는 곳은 Heap영역이며 아래 그림과 같다.

### 1. 동작구조

<img width="645" alt="image" src="https://user-images.githubusercontent.com/59176149/233099793-bfad4644-984d-4f88-809c-3ed3b834b803.png">

Survival 영역이 2개가 존재하는데 이때 Survivor영역의 둘 중 하나는 꼭 비어 있어야 한다.

1. 새로 생성된 객체는 Eden 영역에서 생성이 된다.
2. Eden 영역이 다 차게되면 Minor GC가 발동이 되고, 살아있는 객체(Reachable)는 Survivor 0 영역으로 이동하면서 각자 객체가 가지고 있는 Age-bit를 증가시킨다.
3. 다시 Eden 영역이 다 차게 되면, Eden 영역과 Survivor 영역에서 Minor GC가 발동되고, 살아있는 객체(Reachable) 모두 Survivor 1 영역으로 이동하면서 Age-bit를 증가한다.
4. 1~3 번이 반복 된다.
5. 정해진 임계치(Age-bit)까지 살아남은 객체는 Old 영역으로 이동하게 된다.
6. Old 영역이 다 차게되면 Major GC가 동작하게 된다.

### 2. Minor GC랑 Major GC는 무슨 차이지?

### - Minor GC

- Young 영역에서 발생하는 GC
- 객체 할당률이 높을 수록 Minor GC는 자주 발생
- 일반적으로 Minor GC는 stop-the-world가 발생하지 않는다고 생각하지만, 똑같이 스레드를 중지시킵니다.
- 보통 Major GC보단 빠르지만, 새로 생성된 객체가 GC에 적합하지 않을 경우, Minor GC 소요 시간이 느려질 수 있다.

### - Major GC

- Major GC는 Old 영역에서 발생하는 GC
- Minor GC에 비해 속도가 매우 느려 성능과 안정성에 문제를 끼칠 수 있다.

### 3. young영역과 old영역 왜 나눠놨지?


<img width="645" alt="image" src="https://user-images.githubusercontent.com/59176149/233099793-bfad4644-984d-4f88-809c-3ed3b834b803.png">


GC를 동작하게 되면 **Stop-The-World** 즉, 진행 중인 모든 쓰레드를 중지시키고 GC를 실행시킨다.

이는 객체가 많으면 많을수록 모든 GC를 처리하는 데 아주 오랜 시간이 걸리고 이는 성능에 큰 이슈를 범하게 된다.

GC 설계자들은 어플리케이션을 분석해보니 대부분의 객체가 위 그림과 같이 수명이 짧다는 것을 알 수 있었다.

- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.
- 대부분의 객체들은 금방 GC 의 대상으로 바뀐다.

이러한 연구를 통해 나온 가설을 '**약한 세대 가설(weak generational Hypothesis)**' 이라 부른다.

## 5. 결론

- 메모리를 수동으로 관리하였다면 굉장히 번거로운 일이며 메모리 누수의 위험도 존재한다.
- 이러한 처리를 GC가 알아서 처리해주기에 메모리 관리를 좀 더 편하게 할 수 있는 것이다.
- GC방식과 튜닝에 대해서는 정리하여 링크를 남기도록 하자.
- GC튜닝을 생각한다면 GC튜닝을 최종단계로 생각하고 코드레벨에서부터 객체 생성을 줄이려고 해야 한다.

### 참고

- [https://ko.wikipedia.org/wiki/쓰레기_수집_(컴퓨터_과학)](https://ko.wikipedia.org/wiki/%EC%93%B0%EB%A0%88%EA%B8%B0_%EC%88%98%EC%A7%91_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))
- [https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)
- [https://www.youtube.com/watch?v=FMUpVA0Vvjw](https://www.youtube.com/watch?v=FMUpVA0Vvjw)
- [https://kdhyo98.tistory.com/78](https://kdhyo98.tistory.com/78)
