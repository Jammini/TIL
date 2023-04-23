# **GC 방식에는 어떤 것들이 있을까?**

### 목차

1. [개요](#1-개요)
2. [Serial GC](#2-serial-gc)
3. [Parallel GC](#3-parallel-gc)
4. [Parallel Old GC(Parallel Compacting GC)](#4-parallel-old-gcparallel-compacting-gc)
5. [Concurrent Mark & Sweep GC(이하 CMS)](#5-concurrent-mark--sweep-gc이하-cms)
6. [G1(Garbage First) GC](#6-g1garbage-first-gc)
7. [결론](#7-결론)

### 1.  개요

Old 영역은 기본적으로 데이터가 가득 차면 GC를 실행하는데 이때 GC방식에 따라 처리 절차가 달라진다. 어떤 GC방식이 있는지 살펴보고 이해해보자.

### 2. Serial GC

<img width="375" alt="image" src="https://user-images.githubusercontent.com/59176149/233845281-f29717b0-177d-4391-add5-327332115c5d.png">

- 운영서버에서 절대 사용하면 안되는 방식이며 데스크톱의 CPU 코어가 하나만 있을때 사용하기 위해 만든 방식이다.
- **하나의 쓰레드로 GC를 실행하기에 Stop-The-World 시간이 길어**지므로 애플리케이션의 성능이 많이 덜어진다.
- Serial GC는 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식이다.

### 3. Parallel GC

<img width="454" alt="image" src="https://user-images.githubusercontent.com/59176149/233845291-da76e1b3-3528-43fa-a73d-6fff1c8a0958.png">

- 자바8의 default GC 방식이다.
- Serial GC와 기본적인 알고리즘은 같지만 Serial GC는 GC를 처리하는 스레드가 하나인 것에 비해, Parallel GC는 GC를 처리하는 **쓰레드가 여러개**이다. 다. 그렇기 때문에 Serial GC보다 빠르게 객체를 처리할 수 있다.
- Parallel GC는 메모리가 충분하고 코어의 개수가 많을 때 유리하다

### 4. **Parallel Old GC(Parallel Compacting GC)**

- Parallel Old GC는 JDK 5 update 6부터 제공한 GC 방식
- 앞서 설명한 Parallel GC와 비교하여 Old 영역의 GC 알고리즘만 다르다.
- 이 방식은 Mark-Summary-Compaction 단계를 거친다.
    - Summary 단계는 앞서 GC를 수행한 영역에 대해서 별도로 살아 있는 객체를 식별한다는 점에서 Mark-Sweep-Compaction 알고리즘의 Sweep 단계와 약간 다르며 좀더 복잡하다.

### 5. Concurrent Mark & Sweep GC(이하 CMS)

<img width="511" alt="image" src="https://user-images.githubusercontent.com/59176149/233845366-569d0b8b-b7ff-4c56-9794-767cf0f7b50c.png">

- 초기 Initial Mark 단계에서는 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾는 것으로 끝내 멈추는 시간이 매우 짧다
- 이러한 단계로 진행되는 GC 방식이기 때문에 Stop-The-World 시간이 매우 짧다. 모든 애플리케이션의 응답 속도가 매우 중요할 때 CMS GC를 사용하며, Low Latency GC라고도 부른다.
- CMS GC는 Stop-The-World 시간이 짧다는 장점에 반해 아래와 같은 단점이 존재한다
    - 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.
    - Compaction 단계가 기본적으로 제공되지 않는다.

### 6. G1(Garbage First) GC

<img width="594" alt="image" src="https://user-images.githubusercontent.com/59176149/233845378-c72e075b-695d-480b-bbb2-e9d21cfacb7a.png">

- G1(Garbage First) GC는 Heap을 일정 크기의 Region으로 나누어서 어떤 영역은 Young 영역, 어떤 영역은 Old 영역으로 나눠 활용한다.
- 런타임에 G1 GC가 필요에 따라 영역별 Region 개수를 튜닝하며 그에 따라 Stop-The-World를 최소화 하였다.
- 자바9 이상부터 G1 GC를 기본 GC 실행방식으로 사용.

### 7. 결론

- 2010년 JavaOne에서 Oracle JVM을 만드는 엔지니어들은 이와 같이 말한다.
    - 각 서비스의 WAS에서 생성하는 객체의 크기와 생존 주기가 모두 다르고, 장비의 종류도 다양하다. WAS의 스레드 개수와 장비당 WAS 인스턴스 개수, GC 옵션 등은 지속적인 튜닝과 모니터링을 통해서 해당 서비스에 가장 적합한 값을 찾아야 한다.
- 즉, GC 방식 및 튜닝을 한다는 것은 최종단계에서 이루어져야 하며 GC 상황과 모니터링을 통해 트레이드 오프를 고려해가면 성능적으로 분석한 후 신중하게 고려해봐야할 것이다.

### 참고

- [https://www.youtube.com/watch?v=FMUpVA0Vvjw](https://www.youtube.com/watch?v=FMUpVA0Vvjw)
- [https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)
