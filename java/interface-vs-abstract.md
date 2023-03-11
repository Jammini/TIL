# 인터페이스 vs 추상클래스 (자바8 이후의 변화)

## 목차

1. [인터페이스란?](#1-인터페이스란)
2. [추상클래스란?](#2-추상클래스란)
3. [interface vs abstract class](#3-interface-vs-abstract-class)
4. [결론](#4-결론)

## 개요

- 자바8 이후 interface도 default 키워드를 이용해 메소드의 구현부를 가질 수 있게 되었다.
- 따라서, 추상클래스와 인터페이스간의 차이가 모호해졌다.

## 1. 인터페이스란?

- 추상화된 타입으로, 메소드만을 정의하고 구현은 하지 않은 추상메소드와 상수만들 가지는 일종의 추상클래스이다.
- 자바 8 이전에는 인터페이스의 구현부가 없었으나 이후 부터는 default 키워드를 통해 가능하다.
- 다중 상속을 지원하며, 다른 클래스나 인터페이스가 인터페이스를 구현하여 그 인터페이스의 메소드를 각각 구현가능하다. 인터페이스를 구현하는 클래스는 반드시 인터페이스가 정의한 모든 메소드를 구현해야 한다.
- 인터페이스를 사용하면 객체지향 프로그래밍에서 유연성과 확장성을 높일 수 있다.

## 2. 추상클래스란?

- 하나 이상의 추상 메소드를 포함하는 클래스를 말하며 추상메소드는 메소드만을 정의하고 구현은 하지 않은 메소드다.
- 추상클래스는 일반 클래스와 마찬가지로 필드, 생성자, 멤버 메소드 등을 가질 수 있고, 인터페이스와는 달리 상수를 가질 수 있다.
- 직접 인스턴스화 할 수 없고, 상속받아 하위 클래스에서 구현되어야 한다. 그 하위 클래스는 추상메서드를 반드시 구현해야한다.

## 3. interface vs abstract class

### 1. 상태

- 추상 클래스는 상태를 가질 수 있으며 메서드는 구현부의 상태에 접근이 가능하다.
- 인터페이스에서는 기본 메서드는 허용하나 구현부에서 상태에 접근이 불가능하다.

예제코드를 보자

**1-1. abstact**

```java
public abstract class CircleClass {
		// CircleClass의 상태를 지정하는 color라는 변수가 있다. 
    private String color; 
		// 허용된 색상을 담은 리스트
    private List<String> allowedColors = Arrays.asList("RED", "GREEN", "BLUE");

    public boolean isValid() {
        if (allowedColors.contains(getColor())) {
            return true;
        } else {
            return false;
        }
    }

    //standard getters and setters
}
```

```java
public class ChildCircleClass extends CircleClass {
}
```

- 인스턴스를 만들고 색상을 확인해보자.

```java
CircleClass redCircle = new ChildCircleClass();
redCircle.setColor("RED");
assertTrue(redCircle.isValid()); // 초록불
```

- CircleClass 객체의 color에 접근하여 인스턴스에 유효한 색상이 포함되어 있는지 확인이 가능

**1-2. interface**

```java
public interface CircleInterface {
    List<String> allowedColors = Arrays.asList("RED", "GREEN", "BLUE");

    String getColor();
    
    public default boolean isValid() {
        if (allowedColors.contains(getColor())) {
            return true;
        } else {
            return false;
        }
    }
}
```

- 인터페이스는 상태를 가질 수 없으므로 기본메서드는 상태에 접근이 불가능하다.
- ChidlCircleInterfaceImpl 클래스에서 인스턴스의 상태를 제공하기 위해 getColor() 메서드를 재정의.

```java
public class ChidlCircleInterfaceImpl implements CircleInterface {
    private String color;

    @Override
    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }
}
```

- 인스턴스를 만들고 색상을 확인해보자.

```java
ChidlCircleInterfaceImpl redCircleWithoutState = new ChidlCircleInterfaceImpl();
redCircleWithoutState.setColor("RED");
assertTrue(redCircleWithoutState.isValid()); // 초록불
```

위와 같이 absract클래스에서는 내부에 존재한 color의 상태를 접근하였지만 인터페이스를 ChidlCircleInterfaceImpl에 재정의된 getColor()를 가지고와 상태를 확인한 것을 볼 수 있다. 

### 2. 생성자

- 추상클래스
    - 생성자를 가질 수 있으므로 생성시 상태를 초기화 할 수 있다
- 인터페이스
    - 생성자가 없다.

### 3. 구문상의 차이점

- 추상클래스
    - Object 클래스 메서드를 재정의 O
    - 가능한 모든 접근제어자와 함께 인스턴스 변수를 선언 가능.
    - 인스턴스와 정적 블록 선언 가능.
    - 람다식 참조 불가능.
- 인터페이스
    - Object 클래스 메서드를 재정의 X
    - public, static, final 변수만 가질 수 있고 인스턴스 변수로 불가능
    - 인스턴스와 정적 블록 둘 중 하나를 가질 수 없음.
    - 람다식 참조 가능 단일 추상 메서드를 가질 수 있음.

## 4. 결론

- 자바8 이전에는 추상클래스와 인터페이스의 차이가 가장 크게 느껴진 부분은 메소드 구현부의 유무였을테지만, 8 이후부터는 default 키워드로 추상클래스와 인터페이스가 매우 유사하게 느껴지는게 사실이다.
- 가능하면 클래스를 확장하고 인터페이스를 구현할 수 있으므로 가능하면 default 메서드가 있는 인터페이스를 선택해야 한다.

### 참고

- [https://www.baeldung.com/java-interface-default-method-vs-abstract-class](https://www.baeldung.com/java-interface-default-method-vs-abstract-class)
