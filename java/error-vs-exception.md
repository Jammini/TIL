# 에러와 예외 (Checked exception vs Unchecked exception)

### 목차

1. [에러(error)](#1-에러error)
2. [예외(Exception)](#2-예외exception)

    2-1. [Checked Exception](#2-1-checked-exception)
    
    2-2. [Unchecked Exception](#2-2-unchecked-exception)
    
3. [결론](#3-결론)

### 1. 에러(error)

- 프로그램 코드에 의해서 수습될 수 없는 심각한 오류.
    - 즉, 내가 자바 애플리케이션이 동작하는 환경에서 문제가 생긴 경우.
    - ex) 메모리 부족, 컴퓨터 전기가 나가는 현상 등등..
- 컴파일 에러(Compile error)
    - 컴파일할 때 발생하는 에러
- 런타임 에러(Runtime error)
    - 실행할 때 발생하는 에러

### 2. 예외(Exception)

- 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류.
    - 즉, 내가 짠 코드가 의도한거와 다른 상황에 직면했을 경우.
    - ex) 파일을 읽으려 했는데 없다거나, 예상하지 못한 값이 들어와 계산되는 등등..
- Checked Exception
- Unchecked Exception

<img width="703" alt="image" src="https://user-images.githubusercontent.com/59176149/224531630-ec3bc91f-24d3-4572-ad44-425cf9d8f653.png">


### 2-1. Checked Exception

- `RuntimeException`을 제외한 나머지 애들.
- 무심코 조치를 하지 않았을 경우를 대비해 컴파일러가 check를 해주는 것이며 **반드시 예외처리**를 해주어야만 한다.

<img width="550" alt="image" src="https://user-images.githubusercontent.com/59176149/224531589-1586115d-34e5-4269-afcc-84c2444388c8.png">

- ‘data.txt’ 파일에 쓰기 작업을 하려고 `FileWriter` 클래스를 사용하려고 하면 위와 같이 예외를 처리하지 않았다는 경고를 볼 수 있다.
    - **즉, 반드시 예외처리를 해줘야 정상적으로 컴파일을 할 수 있다!**


<img width="550" alt="image" src="https://user-images.githubusercontent.com/59176149/224531609-f6a3759d-48b0-4951-bfd6-98d33659408e.png">


- 예외처리를 해주니 컴파일이 가능해진 상태가 되며 실행도 정상적으로 되는 것을 볼 수 있다.

### 2-2. Unchecked Exception

- `RuntimeException`을 포함한 자식들.
- 예외처리를 안해줘도 컴파일이 가능하다. 그래서 Exception이 발생할때 그때 에러가 발생한다.

<img width="550" alt="image" src="https://user-images.githubusercontent.com/59176149/224531643-daac0853-bf65-4f4f-a06c-e0933c7f574b.png">

- 위 그림에서 `System.*out*.println(2 / 0)`에서 `ArithmeticException`이 발생하는 코드가 있어도 컴파일단계에서 체크를 해주지 못하는 것을 볼 수 있다.

### 3. 결론

- 에러와 예외를 구분해야 한다.
- 특히, 예외에서는 RuntimeException을 상속하지 않고 꼭 예외처리를 해줘야하는 Checked Exception과 명시적으로 예외처리를 하지 않아도 되는 Unchecked Exception을 구분된다.

### 참고

- [자바의 정석](http://www.yes24.com/Product/Goods/24259565)
- [https://www.youtube.com/watch?v=q80kLBw04gI&t=32s](https://www.youtube.com/watch?v=q80kLBw04gI&t=32s)

