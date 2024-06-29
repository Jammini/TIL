# 자바의 람다식은 왜 등장했을까?

### 목차

1. [개요](#1-개요)
2. [코드 간결성 및 가독성 향상](#2-코드-간결성-및-가독성-향상)
3. [병렬 처리 및 스트림 API와의 통합](#3-병렬-처리-및-스트림-api와의-통합)
4. [함수형 인터페이스와의 연계](#4-함수형-인터페이스와의-연계)
5. [코드 재사용성 및 유지보수성 향상](#5-코드-재사용성-및-유지보수성-향상)
6. [결론](#6-결론)

### 1. 개요

자바의 람다식(Lambda Expression)은 함수형 프로그래밍 개념을 도입하여 자바의 생산성을 높이고, 코드의 가독성과 간결성을 개선하기 위해 등장했다. 람다식은 자바 8에서 도입되었다.

### 2. 코드 간결성 및 가독성 향상

자바 8 이전에는 익명 내부 클래스(Anonymous Inner Classes)를 사용하여 함수형 프로그래밍을 흉내내는 방식으로 코딩해야 했다. 이는 코드가 장황해지고 가독성이 떨어지는 문제를 초래했다. 람다식을 사용하면 다음과 같은 간결하고 가독성 높은 코드를 작성할 수 있다.

### ex) 익명 내부 클래스 vs 람다식

**1) 익명 내부 클래스 사용**

```java
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(new Consumer<String>() {
    @Override
    public void accept(String s) {
        System.out.println(s);
    }
});

```

**2) 람다식 사용**

```java
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(s -> System.out.println(s));
```

### 3. 병렬 처리 및 스트림 API와의 통합

자바 8에서는 스트림 API도 도입되었는데, 람다식은 이 스트림 API와 자연스럽게 통합되어 강력한 데이터 처리 기능을 제공한다. 스트림 API는 컬렉션의 요소들을 함수형 방식으로 처리할 수 있도록 도와주며, 병렬 처리를 쉽게 구현할 수 있게 한다.

### ex) 스트림 API와 람다식

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
                 .filter(n -> n % 2 == 0)
                 .mapToInt(n -> n)
                 .sum();
```

### 4. 함수형 인터페이스와의 연계

람다식은 함수형 인터페이스(Functional Interface)와 함께 사용된다. 함수형 인터페이스는 단 하나의 추상 메서드를 가지는 인터페이스로, 람다식은 이를 구현하는 데 사용된다. 자바 8에서 `java.util.function` 패키지는 여러 가지 유용한 함수형 인터페이스를 제공한다.

### ex) 함수형 인터페이스

```java
@FunctionalInterface
public interface MyFunction {
    void apply(String s);
}

// 람다식을 사용하여 MyFunction 인터페이스를 구현
MyFunction func = (s) -> System.out.println(s);
func.apply("Hello, Lambda!");
```

### 5. 코드 재사용성 및 유지보수성 향상

람다식을 사용하면 코드의 재사용성과 유지보수성이 향상된다. 람다식은 메서드를 간결하게 표현할 수 있기 때문에, 중복 코드를 줄이고, 공통 기능을 보다 쉽게 추출하여 재사용할 수 있다.

### 6. 결론

- 자바의 람다식은 자바를 더욱 현대적이고, 간결하며, 함수형 프로그래밍 스타일을 채택할 수 있게 해주는 중요한 기능이다.
- 이를 통해 개발자는 더 읽기 쉽고 유지보수하기 쉬운 코드를 작성할 수 있으며, 스트림 API와 결합하여 효율적인 데이터 처리를 할 수 있다.