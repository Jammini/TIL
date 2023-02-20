# **Short-circuit이 런타임 오류를 방지해준다?**

간단하게 아래 목록을 천천히 살펴보자.

1. [논리연산자란?](#1-논리연산자란)
2. [Short-circuit을 살펴보자](#2-short-circuit을-살펴보자)
3. [런타임 오류 방지](#3-런타임-오류-방지)
4. [요약](#4-요약)
5. [참고](#참고)

### 1. 논리연산자란?

- && : AND 결합. Conditional AND
- || : OR 결합. Conditional OR


| x | y | x && y | x ll y |
| --- | --- | --- | --- |
| true | true | true | true |
| true | false | false | true |
| false | true | false | true |
| false | false | false | false |


간단히 말해,

AND 결합은 두 개의 조건이 모두 true일 때에만 true.

OR 결합은 둘 중 하나라도 true 이면 true.


### 2. Short-circuit을 살펴보자

- 첫 번째 인수가 값을 결정하기에 충분하지 않은 경우에만 두 번째 인수가 실행되거나 평가한다.
1. OR연산을 쉽게 이해 하기 위해 예제 코드를 보자.
    
    ```java
    int x = 0;
    int y = 3;
    if (x == 0 || x == y) {
        System.out.println("first case");
    } else {
        System.out.println("second case");
    }
    ```
    
- 아래 그림과 같이 `x == 0` 일때 true를 반환하기 때문에 다음 조건인 `x == y`가 true가 되든 false가 되든 조건문은 true가 되는 것을 알 수 있다.
    
    <img width="333" alt="image" src="https://user-images.githubusercontent.com/59176149/220133357-1a4cc06c-0f78-458c-a773-adc4283769fa.png">

- 즉 우리는 OR 연산을 다룰때 아래와 같이 다룰 수 있다. **오른쪽을 검증할 필요가 사라지는 것이다.**
    - (true || wharever) = true

1. AND 연산을 쉽게 이해 하기 위해 예제 코드를 보자.
    
    ```java
    int x = 0;
    int y = 3;
    if (x == 5 && x * y == 0) {
        System.out.println("first case");
    } else {
        System.out.println("second case");
    }
    ```
    
- 아래 그림과 같이 첫번째 조건 `x == 5` 일때 false를 반환하기 때문에 다음 조건인 `x * y == 0`가 true가 되든 false가 되든 조건문은 false가 되는 것을 알 수 있다.
    
    <img width="333" alt="image" src="https://user-images.githubusercontent.com/59176149/220133663-0e17bedd-a89a-4e27-90e1-062eba163b71.png">
    
- 즉 우리는 AND 연산을 다룰때 아래와 같이 다룰 수 있다. **오른쪽을 검증할 필요가 사라지는 것이다.**
    - (false || wharever) = false

### 3. 런타임 오류 방지

바로 코드로 살펴보자

```java
int x = 5;
int y = 0;
if (x == 5 || x / y == 5) {
    System.out.println("first case");
} else {
    System.out.println("second case");
}
```

- 우리는 `x == 5` 가 true 이므로 오른쪽 연산은 할 필요가 사라진 것을 알 수 있다.
- 만약 if문 안에 조건을 다 확인 하면 어떤 일이 벌어 질까?
    - 런타임 에러 발생 → Exception in thread "main" java.lang.ArithmeticException: / by zero

### 4. 요약

- 위의 예시와 같이 간단한 코드지만 Short circuit은 `NullPointerException`, `RuntimeException` 등 과 같은 다양한 오류를 방지하고 연산을 줄일 수 있는데 매우 효과적인 기능이다.
- 따라서, 계산을 고려해 비용이 높은 코드는 나중에 평가되도록 사용하는데 효과적이다.

### 참고

- [위키피디아](https://en.wikipedia.org/wiki/Short-circuit_evaluation)
- [Youtube Short Circuiting](https://www.youtube.com/watch?v=ui_PM-woLsE&ab_channel=CedricSirianni)

