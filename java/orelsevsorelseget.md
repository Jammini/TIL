# Optional – orElse() vs orElseGet()

### 목차

1. [Optional 배경](#1-배경)
2. [Optional 이란?](#2-optional-이란)
3. [orElse() vs orElseGet()](#3-orelse-vs-orelseget)
4. [Supply<T> interface](#4-supply-interface)
5. [성능영향측정](#5-성능-영향-측정)
6. [결론](#6-결론)

### 1. 배경

시작하기에 앞서, 개발자 세상에선 엄청나게 무서운 오류 `NPE`가 존재한다.

개발자는 기계가 아니고 사람인지라 Null 체크 하는 부분을 쉽사리 뺴고 코드를 작성하곤 한다.

기존 조건문을 통해 Null 체크하는 부분을 **자바 8 이후 부터 좀 더 명시적으로 체크하는 Optional이 추가 되었다.**

### 2. Optional 이란?

- 객체를 편리하게 처리하기 위해서 만든 클래스
- 예상치 못한 `NPE`를 제공되는 메소드로 간단히 회피가 가능.
    - 즉, 복잡한 조건문 없이도 널(Null) 값으로 인해 발생하는 예외 처리.

### 3. orElse() vs orElseGet()

1. orElse()

![image](https://user-images.githubusercontent.com/59176149/223495950-1fad8753-624d-4712-98d1-6eaf0c3751d9.png)


- 값이 존재하면 값을 반환하고, 그렇지 않으면 다른 값을 반환한다.

2. orElseGet()

![image](https://user-images.githubusercontent.com/59176149/223496141-3fda4e7f-d52a-4a6c-8bbc-2942d148f286.png)


- 값이 존재하면 값을 반환하고, 그렇지 않으면 supplying 함수가 생성한 결과를 반환한다.

3. orElse() vs orElseGet()

- 둘은 비슷해보이지만 아래 코드를 보며 차이점을 살펴보자

3-1. value가 Null이 아닌 경우.

```java
public class OptionalExample {
    public static void main(String[] args) {
        String name = "jammini";
        String orElseTest = Optional.ofNullable(name).orElse(method());
        System.out.println(orElseTest);

        String orElseGetTest = Optional.ofNullable(name).orElseGet(() -> method());
        System.out.println(orElseGetTest);
    }
    private static String method() {
        System.out.println("come in method");
        return "method";
    }
}
```

출력결과는 아래와 같다.

```
come in method
orElse
orElseGet
```

- `orElse(method())` 는 실행되어  method() 함수가 실행되었지만 `orElseGet(() -> method()`는 method()가 실행되지 않았다.

3-2. value가 null인 경우

```java
public class OptionalExample {
    public static void main(String[] args) {
        String orElse = null;
        String orElseTest = Optional.ofNullable(orElse).orElse(method());
        System.out.println(orElseTest);

        String orElseGet = null;
        String orElseGetTest = Optional.ofNullable(orElseGet).orElseGet(() -> method());
        System.out.println(orElseGetTest);
    }
    private static String method() {
        System.out.println("come in method");
        return "method";
    }
}
```

출력 결과는 아래와 같다.

```
come in method
method
come in method
method
```

- 위 코드를 보면 이렇게 정리할 수 있다
    - orElse() : 해당 값이 null 이든 아니든 관계없이 항상 불린다.
    - orElseGet() : 해당 값이 null일 경우에만 불린다.

**이유는 orElseGet()의 params에 있는 Supplier 녀석이 Lazy Evaluation이 가능하기 때문이다. 조금 더 깊이 알고 싶다면 [4. Supply<T>](#4-supply-interface) 에서 좀 더 자세히 살펴보자.**

### 4. Supply<T> interface

![image](https://user-images.githubusercontent.com/59176149/223496336-13083916-509d-4c0a-9ca6-bffe08b99c01.png)


- 단순히 무엇인가를 반환하는 메소드만 존재한다.
- get() 메소드를 통해 Lazy Evaluation 이 가능하다

코드를 통해 확실히 살펴보자.

1. Supplier 사용 X

```java
public class SupplierExamples {

    /**
     * 실행하면 항상 3초가 걸려요.
     */
    private static String getVeryExpensiveValue() {
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "Jammini";
    }
    /**
     * number에 조건식에 따라 출력문이 달라짐.
     */
    private static void printIfValidIndex(final int number, final String value) {
        if (number >= 0) {
            System.out.println("The value is " + value + ".");
        } else {
            System.out.println("Invalid");
        }
    }

    private static void callingExpensiveMethodWithoutSupplier() {
        final long start = System.currentTimeMillis();
        printIfValidIndex(0, getVeryExpensiveValue());
        printIfValidIndex(-1, getVeryExpensiveValue());
        printIfValidIndex(-2, getVeryExpensiveValue());
        System.out.println("It took " + ((System.currentTimeMillis() - start) / 1000) + " seconds.");
    }		
		
    public static void main(final String[] args) {
        callingExpensiveMethodWithoutSupplier();
    }
}
```

출력은 다음과 같다

```
The value is jammni.
Invalid
Invalid
It took 9 seconds.
```

- printIfValidIndex에 number의 조건과 관계없이 getVeryExpensiveValue() 메소드가 3번 호출되어 9초가 경과된다.
1. Supplier 사용 O

```java
public class SupplierExamples {

    /**
     * 실행하면 항상 3초가 걸려요.
     */
    private static String getVeryExpensiveValue() {
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "Jammini";
    }
    /**
     * number에 조건식에 따라 출력문이 달라짐.
     */
    private static void printIfValidIndex(final int number, final Supplier<String> valueSupplier) {
        if (number >= 0) {
            System.out.println("The value is " + valueSupplier.get() + ".");
        } else {
            System.out.println("Invalid");
        }
    }

    private static void callingExpensiveMethodWithSupplier() {
        final long start = System.currentTimeMillis();
        printIfValidIndex(0, () -> getVeryExpensiveValue());
        printIfValidIndex(-1, () -> getVeryExpensiveValue());
        printIfValidIndex(-2, () -> getVeryExpensiveValue());
        System.out.println("It took " + ((System.currentTimeMillis() - start) / 1000) + " seconds.");
    }	
		
    public static void main(final String[] args) {
        callingExpensiveMethodWithSupplier();
    }
}
```

출력은 다음과 같다

```
The value is jammni.
Invalid
Invalid
It took 3 seconds.
```

- 이번에는 printIfValidIndex에 number의 조건문이 성립할때만 실제적으로 valueSupplier.get()을 호출 할때만 getVeryExpensiveValue() 메소드를 호출하게 되어 3초가 경과 된 것을 볼 수 있다.

**위 코드의 결과를 보면 불필요한 연산을 지연시키는 Lazy Evaluation이 일어나 물필요한 연산을 피하는 것을 알 수 있었다.**

### 5. 성능 영향 측정

- [baeldung](https://www.baeldung.com/java-optional-or-else-vs-or-else-get#jmh%20benchmark) 에서 orElse()와 orElseGet()을 JMH을 이용해 성능에 대해 수치화 한 결과를 보자.

```
Benchmark           Mode  Cnt      Score       Error  Units
orElseBenchmark     avgt   20  60934.425 ± 15115.599  ns/op
orElseGetBenchmark  avgt   20      3.798 ±     0.030  ns/op
```

- 위와 같이 성능에 엄청나게 영향을 미치는 것을 볼 수 있다.

### 6. 결론

- 위와 같이 필요에 따라 orElse()와 orElseGet()을 신중하게 결정해서 사용하는 것이 중요하다.
- 기본적으로 기본 객체가 이미 생성되어 직접 엑세스 할 수 있는 경우가 아니라면 orElseGet()을 사용하는 것이 더 효율적인 선택이 될 수 있다.

### 참고

- [https://docs.oracle.com/javase/8/docs/api/](https://docs.oracle.com/javase/8/docs/api/)

- [https://www.baeldung.com/java-optional-or-else-vs-or-else-get](https://www.baeldung.com/java-optional-or-else-vs-or-else-get)

- [https://m.blog.naver.com/zzang9ha/222087025042](https://m.blog.naver.com/zzang9ha/222087025042)
