# JDK15에서 정식으로 인정된 ZGC

### 목차

1. [개요](#1-개요)
2. [ZGC 메모리구조](#2-zgc-메모리구조)
    
    2-1. [ZPage](#2-1-zpage)
    

### 1. 개요

ZGC(Z Garbage Collector)는 Serial GC와 Parallel Old GC, Parallel GC, CMS GC, G1 GC를 지나 새로운 세대로 등장한 Java의 garbage collector다.

ZGC는 JDK 11에 실험적 기능으로 추가되었고, JDK 15에서 정식으로 garbage collector로 인정된 다음 LTS(long term support) 버전인 JDK 17에도 반영되었다.

### 2. ZGC 메모리구조

GC(garbage collection)의 목적은 메모리를 정리하는 것이다. ZGC의 흐름을 알기 위해선 메모리 구조를 아는 것이 중요하다.

### 2-1. ZPage

이전 세대의 garbage collector인 G1 GC에서는 메모리를 region이라는 논리적인 단위로 구분한다.

새로운 세대의 garbage collector인 ZGC에서는 메모리를 ZPage라는 논리적인 단위로 구분한다.

OpenJDK의 소소 코드에 구현된 ZPage의 구조는 다음과 같다(코드 출처: https://github.com/openjdk/jdk/blob/master/src/hotspot/share/gc/z/zPage.hpp).

ZPage의 3가지 타입은 small과 medium, large이다(코드 출처:

```java
enum class ZPageType : uint8_t {  
  small,
  medium,
  large
};
```

```java
//  Page Type     Page Size     Object Size Limit     Object Alignment
//  ------------------------------------------------------------------
//  Small         2M            <= 265K               <MinObjAlignmentInBytes>
//  Medium        32M           <= 4M                 4K
//  Large         X*M           > 4M                  2M
//  ------------------------------------------------------------------
```

각 타입별로 들어갈 수 있는 객체의 크기가 제한되는데 ZPage로 구분된 ZGC heap 영역의 메모리 구조를 보면 이해하기 쉽다.

<img width="888" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3a0075af-3243-4c12-9a95-43ae18c3fd05">

ZPage를 사용할 때 주의할 점은 large 타입의 ZPage에는 단 하나의 객체만 할당할 수 있다는 것이다. 그렇기 때문에 5MB 크기의 객체를 large 타입의 ZPage에 할당하면 large 타입 ZPage의 크기가 medium 타입 ZPage의 크기보다 작을 수 있다.

- Java 15에 release
- 대량의 메모리(8MB ~ 16TB)를 low-latency로 잘 처리하기 위해 디자인 된 GC
- G1의 Region 처럼,  ZGC는 ZPage라는 영역을 사용하며, G1의 Region은 크기가 고정인데 비해, ZPage는 2mb 배수로 동적으로 운영됨. (큰 객체가 들어오면 2^ 로 영역을 구성해서 처리)
- ZGC가 내세우는 최대 장점 중 하나는 힙 크기가 증가하더도 'stop-the-world'의 시간이 절대 10ms를 넘지 않는다는 것

### 참고
- https://d2.naver.com/helloworld/0128759