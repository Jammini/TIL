# try-with-resources로 쉽게 자원해제하기

### 목차

1. [개요](#1-개요)
2. [try-catch-finally란?](#2-try-catch-finally란)
3. [try-with-resource란?](#3-try-with-resource란)
4. [try-with-resources를 사용해야 하는 이유](#4-try-with-resources를-사용해야-하는-이유)
5. [결론](#5-결론)

### 1. 개요

- 자바7 이전에는 다 사용하고 난 자원(resource)를 반납하기 위해 try-catch-finally구문을 사용했다.
- 클라이언트가 자원 반납을 놓치는 경우 예측할 수 없는 성능 문제가 발생되기도 한다.
- 자원을 쉽게 회수할 수 있는 방법으로 try-with-resource가 자바7 부터 등장했다.

### 2. try-catch-finally란?

```java
public static void main(String[] args) {
    FileWriter f = null;
    try {
        f = new FileWriter("data.txt");
        f.write("Hello");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (f != null) {
            try {
                f.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

- Java7 이전에 try-catch-finally 코드를 보면 위와 같이 리소스를 생성하고, 반납한다.
- 리소스의 생성은 try 구문에서, 리소스의 반납은 finally 구문에서 하다보니,
- 리소스를 생성하고, 반납을 빼먹는 경우가 종종 발생했다.

### 3. try-with-resource란?

- 자바 7버전 이후에 추가된 try-with-resources 기능은 try 구문에 리소스를 선언하고, 리소스를 다 사용하고 나면 자동으로 반납(close)해주는 기능이다.
- 그렇다면 모든 자원을 닫아주냐?
    - 아래와 같이 AutoCloseable이라는 인터페이스를 가지고 있다면 try-with-resources 문법이 사용가능하다.

<img width="712" alt="image" src="https://user-images.githubusercontent.com/59176149/228562471-3eea3034-f039-436d-ab7d-2503dbbbe359.png">


- 코드로 보면 다음과 같다.

```java
public static void main(String[] args) {
    try (FileWriter f = new FileWriter("data.txt")) {
        f.write("Hello");
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

- 이 코드는 위에 try-catch-finally를 구현한 코드와 똑같이 동작한다.

<img width="669" alt="image" src="https://user-images.githubusercontent.com/59176149/228562809-6985cf7b-6a06-4a2e-a200-2c5a423dea72.png">

- 그림과 같이 close가 중복이라고 IDE에서 알려주고 있으며 자동으로 내부적으로 자원을 반납 해준다는 것이다.

### 4. ****try-with-resources를 사용해야 하는 이유****

```java
public class FirstError extends RuntimeException {

}
```

```java
public class SecondError extends RuntimeException {
}
```

```java
public class MyResource implements AutoCloseable {
		public void doSomething() {
				System.out.println("Do something");
				throw new FirstError();
		}

		@Override
		public void close() throws Exception {
				System.out.println("Close My Resource");
				throw new SecondError();
		}
}
```

- 위와 같이 코드가 있고 AppRunner에서 코드를 돌려보자

1-1. try-catch-finally 사용

<img width="466" alt="image" src="https://user-images.githubusercontent.com/59176149/228562997-baade6ad-1f04-4e30-812d-91983d0a6d52.png">

- 실행 결과를 보자.

<img width="471" alt="image" src="https://user-images.githubusercontent.com/59176149/228563081-d4365675-e3c0-4c25-9af3-34d445906d64.png">

- doSomething에서 먼저 FisrtError가 났으나 마지막 최상단에 SecondError를 나타내고 있다.

1-2. try-with-resource 사용

<img width="533" alt="image" src="https://user-images.githubusercontent.com/59176149/228563179-c50a592b-c50a-46f3-a45b-1886f9b45f73.png">


- 실행 결과를 보자

<img width="455" alt="image" src="https://user-images.githubusercontent.com/59176149/228563284-f8a22993-dd3b-4bfa-8f74-9f72066b8300.png">

- 가장 먼저 에러가 발생한 FirstError뒤에 close를 하면서 SecondError와 같은 에러가 발생하면 뒤에다 쟁여 놓고 첫번째에러를 먼저 보여주고 Suppressed 뒤에 나머지 쟁여놓은 에러를 보여준다.
- 즉, 뒤에 발생한 에러는 첫번째 발생한 에러 뒤에다 쌓아두고(Suppressed) 처음 발생한 에러를 중요시 여긴다.
- 또한 중복으로 try-catch를 만들어야 하는 경우에도 실수를 할 가능성이 높다.

### 5. 결론

- **우리는 아래와 같은 이유로 자바7 이상 버전을 사용하고 있다면 `try-catch-finally`가 아닌 `try-with-resources`를 사용해야 한다.**
1. try-finally와 달리 뒤에 발생한 에러는 첫번째 발생한 에러 뒤에 쌓아두고(Suppressed) 처음 발생한 에러를 중요시 여긴다. 즉, 디버깅이 쉬워진다.
2. 번거로운 자원 반납 작업을 하지 않아도 된다.
3. 코드를 간결하게 만들 수 있다.

### 참고

- [https://www.youtube.com/watch?v=zqjZBSqHs0s](https://www.youtube.com/watch?v=zqjZBSqHs0s)
- [https://mangkyu.tistory.com/217](https://mangkyu.tistory.com/217)
