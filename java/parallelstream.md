# ParallelStream은 항상 Stream보다 성능이 좋을까?

### 목차

1. [개요](#1-개요)
2. [자바의 Stream](#2-자바의-stream)
    
    2-1. [Sequential Streams](#1-sequential-streams)
    
    2-2. [Parallel Streams](#2-parallel-streams)
    
    2-3. [ForkJoinFrameWork](#3-forkjoinframework)
    
    2-3-1. [Splitting Source](#3-1-splitting-source)
    
    2-3-2. [Common Thread Pool](#3-2-common-thread-pool)
    
    2-3-3. [Custom Thread Pool](#3-3-custom-thread-pool)
    
3. [성능에 미치는 영향](#3-성능에-미치는-영향)
    
    3-1. [오버헤드](#1-오버헤드)
    
    3-2. [분할 비용](#2-분할-비용)
    
    3-3. [병합 비용](#3-병합-비용)
    
4. [ParallelStream은 언제 사용하는게 좋을까?](#4-parallelstream은-언제-사용하는게-좋을까)
5. [결론](#5-결론)

## 1. 개요

결론부터 말하면, 항상 성능이 좋다고 판단해서는 안된다.

단순히 보면 작업을 나누어서 실행하면 항상 더 빠르다고 생각할 수 있지만 이는 병렬로 처리하기 때문에 CPU도 많이 사용하고 몇개의 쓰레드로 처리 할지가 보장되지 않는다.

parellelStream 동작 방식과 사용시 고려할 사항에 대해 글을 써보려 한다.

## 2. 자바의 Stream

### 1. ****Sequential Streams****

- 기본적으로 parallel로 명시적으로 사용하지 않는다면 Java의 모든 스트림 작업은 순차적으로 처리된다.

```java
List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
listOfNumbers.stream().forEach(number ->
    System.out.println(number + " " + Thread.currentThread().getName())
);
```

- 아래 출력결과를 보면, 리스트 목록은 순차적으로 출력되는 것을 볼 수 있다.

```
1 main
2 main
3 main
4 main
```

### 2. ****Parallel Streams****

- parallelStream(), parallel()만을 이용해 stream을 병렬 처리할 수 있게 해준다.
- ForkJoinFrameWork를 이용하여 복잡하던 스레드 관리 방식을 Fork와 Join을 통해 작업들을 나누고 조인해준다.

```java
List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
listOfNumbers.parallelStream().forEach(number ->
    System.out.println(number + " " + Thread.currentThread().getName())
);
```

- 아래 출력결과를 보면, 리스트 목록은 별도의 코어에서 병렬로 코드를 실행하므로 실행 순서를 보장해주지 않는다. 그러므로 실행할 때마다 실행 순서는 변경이 되는 것을 확인할 수 있다.

```
4 ForkJoinPool.commonPool-worker-3
2 ForkJoinPool.commonPool-worker-5
1 ForkJoinPool.commonPool-worker-7
3 main
```

### 3. ForkJoinFrameWork

- ForkJoinFrameWork는 자바7에 java.util.concurrent에 추가되어 여러 스레드간의 작업 관리를 처리한다.
- 아래 코드가 parallelStream이 아닌 기존 Stream으로 처리 되었다면 15가 나와야 하지만 그림과 같이 처리가 되기 때문에 15라는 값이 나오지 않는다.

### 3-1. ****Splitting Source****

- 쓰레드 간에 소스데이터를 분할하고 작업을 완료하면 콜백을 처리하는 역할을 한다

```java
List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
int sum = listOfNumbers.parallelStream().reduce(5, Integer::sum);
assertThat(sum).isNotEqualTo(15); // OK
```

<img width="697" alt="image" src="https://user-images.githubusercontent.com/59176149/229744777-a9f4e292-d1d0-42af-a8ea-606f51e8f90c.png">

- 이를 해결하려면 아래와 같이 병렬작업이 끝난 후 +5를 연산해주어야 한다.

```java
List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
int sum = listOfNumbers.parallelStream().reduce(0, Integer::sum) + 5;
assertThat(sum).isEqualTo(15); // OK
```

- 따라서, 병렬로 처리하는 작업에서는 주의해서 작성해야 한다.

### 3-2. ****Common Thread Pool****

- Common pool의 스레드 수는 프로세서 코어의 수 - 1 과 같으나 아래와 같이 JVM 매개변수를 전달해 스레드 수 셋팅할 수 도 있다.

```java
-D java.util.concurrent.ForkJoinPool.common.parallelism=4
```

- 전역 설정이라 Common pool을 사용하는 모든 작업에 영향을 미치기에 변경해야만 하는 이유가 타당하다면 변경하지 않는 것을 권장한다.

### 3-3. ****Custom Thread Pool****

- 쓰레드 수를 지정해서 parallelStream을 이용할 수 도 있으나 oracle에서는 default인 공통 스레드 풀을 사용하는 것을 권장한다.

```java
List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
ForkJoinPool customThreadPool = new ForkJoinPool(4);
int sum = customThreadPool.submit(
    () -> listOfNumbers.parallelStream().reduce(0, Integer::sum)).get();
customThreadPool.shutdown();
assertThat(sum).isEqualTo(10);
```

## 3. 성능에 미치는 영향

- parallelStream을 사용하려고 한다면 다음과 같은 사항들을 고려해봐야 한다.

### 1. 오버헤드

- 아래 두가지 코드에 대해 벤치마크를 통해 성능을 조회해보자

```java
IntStream.rangeClosed(1, 100).reduce(0, Integer::sum);
IntStream.rangeClosed(1, 100).parallel().reduce(0, Integer::sum);
```

- 1부터 100까지의 합을 기존 스트림보다 병렬로 처리하게 되면 성능이 저하된걸 확인할 수 있다.

```
Benchmark                                                     Mode  Cnt        Score        Error  Units
SplittingCosts.sourceSplittingIntStreamParallel               avgt   25      35476,283 ±     204,446  ns/op
SplittingCosts.sourceSplittingIntStreamSequential             avgt   25         68,274 ±       0,963  ns/op
```

- 쓰레드, 소스 및 결과를 관리하는 오버헤드가 실제 작업을 수행하는 것보다 비용이 많이 드는 작업이기 때문이다.
- 컬렉션에 요소의 수가 적고 요소당 처리 시간이 짧으면 순차처리가 오히려 병렬처리보다 빠를 수 있다.
- 쓰레드풀 생성, 쓰레드 생성과 같은 추가적인 비용이 발생하기 때문에 성능적으로 고려해야한다.

### 2. 분할 비용

- 병렬처리를 위해 데이터 소스를 분할하는데, 이 때 어떤 데이터소스이냐에 따라 비용차이가 난다.
- ArrayList나 배열과 같이 인덱스로 접근하는 데이터 같은 경우는 fork단계에서 쉽게 요소를 분리할 수 있지만 HashSet이나 LinkedList같은 경우는 분리하는 작업이 쉽지 않다.
- ArrayList와 LinedList의 벤치마크를 돌려 성능 차이를 살펴보자.

```java
private static final List<Integer> arrayListOfNumbers = new ArrayList<>();
private static final List<Integer> linkedListOfNumbers = new LinkedList<>();

static {
    IntStream.rangeClosed(1, 1_000_000).forEach(i -> {
        arrayListOfNumbers.add(i);
        linkedListOfNumbers.add(i);
    });
}
```

```java
arrayListOfNumbers.stream().reduce(0, Integer::sum)
arrayListOfNumbers.parallelStream().reduce(0, Integer::sum);
linkedListOfNumbers.stream().reduce(0, Integer::sum);
linkedListOfNumbers.parallelStream().reduce(0, Integer::sum);
```

- 결과를 보면 ArrayList에 대해서만 병렬처리가 효과적인걸 볼 수 있다.

```
Benchmark                                                     Mode  Cnt        Score        Error  Units
DifferentSourceSplitting.differentSourceArrayListParallel     avgt   25    2004849,711 ±    5289,437  ns/op
DifferentSourceSplitting.differentSourceArrayListSequential   avgt   25    5437923,224 ±   37398,940  ns/op
DifferentSourceSplitting.differentSourceLinkedListParallel    avgt   25   13561609,611 ±  275658,633  ns/op
DifferentSourceSplitting.differentSourceLinkedListSequential  avgt   25   10664918,132 ±  254251,184  ns/op
```

### 3. 병합 비용

- 병렬 처리를 위해 소스를 분해를 했다면 분해하고 난 후 결과를 합하는 비용도 생각해야 한다.

```java
arrayListOfNumbers.stream().reduce(0, Integer::sum);
arrayListOfNumbers.stream().parallel().reduce(0, Integer::sum);

arrayListOfNumbers.stream().collect(Collectors.toSet());
arrayListOfNumbers.stream().parallel().collect(Collectors.toSet())
```

- 아래 결과와 같이 병렬처리는 합계 연산을 했을때만 성능의 이점이 있는걸 확인 할 수 있다.

```
Benchmark                                                     Mode  Cnt        Score        Error  Units
MergingCosts.mergingCostsGroupingParallel                     avgt   25  135093312,675 ± 4195024,803  ns/op
MergingCosts.mergingCostsGroupingSequential                   avgt   25   70631711,489 ± 1517217,320  ns/op

MergingCosts.mergingCostsSumParallel                          avgt   25    2074483,821 ±    7520,402  ns/op
MergingCosts.mergingCostsSumSequential                        avgt   25    5509573,621 ±   60249,942  ns/op
```

## 4. ParallelStream은 언제 사용하는게 좋을까?

- 많은 양의 데이터와 요소당 처리시간이 많이 걸린다면 병렬처리가 더 좋은 성능을 나타낼 수도 있다.
- 적은양의 데이터나 분할하거나 병합할때 비용이 많이 드는 작업이 발생한다면 좋지 못한 성능은 낼 수 있다.
- 즉, ParallelStream을 사용할때는 위와 같이 고려해야 할 사항들을 체크하고 성능테스트를 진행 한 후최적화된 전략을 이용해 사용을 권장한다.

## 5. 결론

- 자바에서 ParallelStream이 ForkJoinFrameWork를 이용하여 복잡하던 스레드 관리 방식을 처리해준다.
- 병렬스트림이 순차스트림보다 항상 좋은 성능을 나타내지 않는다.
- ParallelStream의 사용은 앞서 본 오버헤드, 분할비용, 병합비용등등 같은 사항들을 고려해 가며 성능테스트를 진행한 후에 사용하라.

### 참고

- [https://www.baeldung.com/java-when-to-use-parallel-stream](https://www.baeldung.com/java-when-to-use-parallel-stream)
- [https://www.youtube.com/watch?v=iyR1Ir2I2rA](https://www.youtube.com/watch?v=iyR1Ir2I2rA)
- [https://duck67.tistory.com/30](https://duck67.tistory.com/30)
