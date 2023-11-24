# 전략패턴(Strategy Pattern)이란?

### 목차

1. [전략 패턴이란?](#1-전략패턴이란)
2. [패턴적용하기](#2-패턴-적용하기)
3. [장점과 단점](#3-장점과-단점)
4. [자바와 스프링에서 찾아보는 패턴](#4-자바와-스프링에서-찾아보는-패턴)

### 1. 전략패턴이란?

전략 패턴은 은연중에 굉장히 자주 사용하는 패턴중 하나이다. 여러 라이브러리나 프레임워크에서 구현하고 있는 패턴이기 때문이다.

전략 패턴은실행(런타임) 중에 알고리즘 전략을 선택하여 객체 동작을 실시간으로 바뀌도록 할 수 있게 하는행위 디자인 패턴 이다.여기서 '전략'이란 일종의 알고리즘이 될 수 도 있으며, 기능이나 동작이 될 수도 있는 특정한 목표를 수행하기 위한 행동 계획을 말한다.즉, 어떤 일을 수행하는 알고리즘이 여러가지 일때, 동작들을 미리 전략으로 정의함으로써 손쉽게 전략을 교체할 수 있는,알고리즘 변형이 빈번하게 필요한 경우에 적합한 패턴이다.

<img width="588" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/27329a25-b309-4d32-99fd-745dd26a93e8">

- ConcreteState : 알고리즘, 행위, 동작을 객체로 정의한 구현체
- Strategy Interface : 모든 전략 구현제에 대한 공용 인터페이스
- Context : 알고리즘을 실행해야 할 때마다 해당 알고리즘과 연결된 전략 객체의 메소드를 호출.
- Client : 특정 전략 객체를 컨텍스트에 전달 함으로써 전략을 등록하거나 변경하여 전략 알고리즘을 실행한 결과를 누린다.

### 2. 패턴 적용하기

1. Before

```java
public class BlueLightRedLight {

    private int speed;

    public BlueLightRedLight(int speed) {
        this.speed = speed;
    }

    public void blueLight() {
        if (speed == 1) {
            System.out.println("무 궁 화    꽃   이");
        } else if (speed == 2) {
            System.out.println("무궁화꽃이");
        } else {
            System.out.println("무광꼬치");
        }

    }

    public void redLight() {
        if (speed == 1) {
            System.out.println("피 었 습 니  다.");
        } else if (speed == 2) {
            System.out.println("피었습니다.");
        } else {
            System.out.println("피어씀다");
        }
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        BlueLightRedLight blueLightRedLight = new BlueLightRedLight(3);
        blueLightRedLight.blueLight();
        blueLightRedLight.redLight();
    }
}
```

Speed의 값에 따라서 다르게 결과를 출력하고 있으며 다른 Speed 값을 주고 싶다면 해당 분기문에 코드 수정이 빈번하게 발생하며 버그 발생율을 높게 된다.

1. After

```java
public interface Speed {
    void blueLight();
    void redLight();
}
```

```java
public class Normal implements Speed {
    @Override
    public void blueLight() {
        System.out.println("무 궁 화    꽃   이");
    }

    @Override
    public void redLight() {
        System.out.println("피 었 습 니  다.");
    }
}
```

```java
public class Faster implements Speed {
    @Override
    public void blueLight() {
        System.out.println("무궁화꽃이");
    }

    @Override
    public void redLight() {
        System.out.println("피었습니다.");
    }
}
```

```java
public class Fastest implements Speed{
    @Override
    public void blueLight() {
        System.out.println("무광꼬치");
    }

    @Override
    public void redLight() {
        System.out.println("피어씀다.");
    }
}
```

```java
public class BlueLightRedLight {

    // 호출 시점에 파라미터로 전략을 넘긴다.
    public void blueLight(Speed speed) {
        speed.blueLight(); // 작업 위임 (이 파일이 변경되도 전략에는 영향이 없다)
    }

    public void redLight(Speed speed) {
        speed.redLight();
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        BlueLightRedLight game = new BlueLightRedLight();

        // 새로운 전략은 클래스 추가 필요 (기존 클래스(BlueLightRedLight)의 수정은 필요 없다)
        game.blueLight(new Normal());
        game.redLight(new Fastest());

        game.blueLight(new Speed() {
            @Override
            public void blueLight() {
                System.out.println("blue light");
            }

            @Override
            public void redLight() {
                System.out.println("red light");
            }
        });
    }
}
```

Speed 라는 인터페이스를 통해 추가되는 새로운 전략들을 해당 인터페이스를 구현하는 구현클래스로 추가된다.

즉, 기존에 구현이 되어 있던 코드들은 수정없이 새로운 전략을 추가 할 수 있다는 것이다.

### 3. 장점과 단점

**장점**

- 새로운 전략을 추가하더라도 기존 코드를 변경하지 않는다.
    - 기존 코드를 손상시키지 않는 것은 객체지향 원칙 중 `OCP(개방/폐쇄 원칙)` 에 해당한다.
- 상속 대신 위임을 사용할 수 있다.
- 런타임에 전략을 변경할 수 있다.

**단점**

- 복잡도가 증가한다.
- 클라이언트 코드가 구체적인 전략을 알아야 한다.

### 4. 자바와 스프링에서 찾아보는 패턴

- 자바의 Comparator
- Spring의 Interface의 대부분 전략패턴이 적용된어 있다.
    - ApplicationContext : ClassPathXmlApplicationContext, FileSystemXmlApplicationContext, AnnotationConfigApplicationContext...

### 참고

- [https://www.inflearn.com/course/디자인-패턴/dashboard](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
