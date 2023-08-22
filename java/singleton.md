# Java 에서 싱글톤패턴 구현방식

### 목차

1. [개요](#1-개요)
2. [Eager Initialization](#2-eager-initialization)
3. [Static Block Initialization](#3-static-block-initialization)
4. [Lazy Initialization](#4-lazy-initialization)
5. [Thread Safe Singleton](#5-thread-safe-singleton)

    5-1. [double checked locking](#5-1-double-checked-locking)

6. [Bill Pugh Singleton Implementaion](#6-bill-pugh-singleton-implementaion)
7. [Enum Singleton](#7-enum-singleton)
8. [결론](#8-결론)

### 1. 개요

싱글톤 패턴은 어떤 **클래스의 인스턴스가 오직 하나만 생성**되는 것을 보장하며, 이 인스턴스에 접근할 수 있는 사용할 수 있도록 제공하는 패턴이다.

싱글톤 패턴을 구현하는 방법을 중점으로 해당 내용을 정리해보려고 한다.

### 2. Eager Initialization

Eager Initialization은 가장 간단한 형태의 구현 방법이다. 이는 싱글톤 클래스의 인스턴스를 클래스 로딩 단계에서 생성하는 방법으로 Thread-safe하다.

그러나 어플리케이션에서 해당 인스턴스를 사용하지 않더라도 인스턴스를 생성하기 때문에 낭비가 발생할 수 있게된다.

```java
public class Singleton {
    
    private static final Singleton instance = new Singleton();//클래스 로딩 시점에 생성
    
    private Singleton(){} //private 생성자로 외부 클래스로부터 인스턴스가 생성되는것을 차단한다.
 
    public static Singleton getInstance(){
        return instance;
    }
}
```

이 방법을 사용할 때는 싱글톤 클래스가 다소 적은 리소스를 다룰 때여야 한다.

File System, Database Connection 등 큰 리소스들을 다루는 싱글톤을 구현할 때는 위와 같은 방식보다는 getInstance() 메소드가 호출될 때까지 싱글톤 인스턴스를 생성하지 않는 것이 더 좋다.

또한, Eager Initializaion은 Exception에 대한 Handling도 제공하지 않는다.

### 3. Static Block Initialization

Static Block Initialization은 Eager Initialization과 유사하지만 static block을 통해서 Exception Handling에 대한 옵션을 제공한다.

```java
public class Singleton {
 
    private static Singleton instance;
    
    private Singleton(){}
    
    //static block initialization for exception handling
    static{
        try{
            instance = new Singleton();
        }catch(Exception e){
            throw new RuntimeException("Exception occured in creating singleton instance");
        }
    }
    
    public static Singleton getInstance(){
        return instance;
    }
}
```

위와 같이 구현할 경우 싱글톤 클래스의 인스턴스를 생성할 때 발생할 수 있는 예외에 대한 처리를 할 수 있지만, Eager Initialization과 마찬가지로 클래스 로딩 단계에서 인스턴스를 생성하기 때문에 여전히 큰 리소스를 다루는 경우에는 적합하지 않게 된다.

### 4. Lazy Initialization

Lazy Initialization은 앞선 두 방식과는 달리 나중에 초기화하는 방법이다.

이는 global access 한 getInstance() 메소드를 호출할 때에 인스턴스가 없다면 생성한다.

```java
public class Singleton {
 
    private static Singleton instance;
    
    private Singleton(){}
    
    public static Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```

이 방식으로 구현할 경우 2, 3번에서 안고 있던 문제(사용하지 않았을 경우에는 인스턴스가 낭비)에 대해 해결책이 된다. 그러나 이 경우에도 문제점이 있다. 그건 바로 **multi-thread 환경에서 동기화 문제**이다.

만약 인스턴스가 생성되지 않은 시점에서 여러 쓰레드가 동시에 getInstance()를 호출한다면 예상치 못한 결과를 얻을 수 있을뿐더러, 단 하나의 인스턴스를 생성한다는 싱글톤 패턴에 위반하는 문제점이 야기될 수 있다.

그렇기에 이 방법으로 구현을 해도 괜찮은 경우는 single-thread 환경이 보장됐을 때의 사용을 권장한다.

### 5. Thread Safe Singleton

Thread Safe Singleton은 4번의 문제를 해결하기 위한 방법으로, getInstance() 메소드에 synchronized를 걸어두는 방식이다.

synchronized 키워드는 임계 영역(Critical Section)을 형성해 해당 영역에 오직 하나의 쓰레드만 접근 가능하게 Thread-safe를 보장해준다.

```java
public class Singleton {
 
    private static Singleton instance;
    
    private Singleton(){}
    
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
    
}
```

위와 같은 방식으로 구현한다면 getInstance() 메소드 내에 진입하는 쓰레드가 하나로 보장받기 때문에 멀티 쓰레드 환경에서도 정상 동작하게 된다.

그러나 synchronized 키워드 자체에 대한 비용이 크기 때문에 싱글톤 인스턴스 호출이 잦은 **어플리케이션에서는 성능이 떨어지게 되는 문제가 발생한다.**

### 5-1. double checked locking

위와 같은 문제를 개선하기 위해 고안된 방식이 double checked locking 이다.

이는 getInstance() 메소드 수준에 lock을 걸지 않고 instance가 null일 경우에만 synchronized가 동작하도록 한다.

```java
public static Singleton getInstance(){
    if(instance == null){
        synchronized (Singleton.class) {
            if(instance == null){
                instance = new Singleton();
            }
        }
    }
    return instance;
}
```

### 6. Bill Pugh Singleton Implementaion

이는 Bill Pugh가 고안한 방식으로, inner static helper class를 사용하는 방식이다.

앞선 방식이 안고 있는 문제점들을 대부분 해결한 방식으로, **현재 가장 널리 쓰이는 싱글톤 구현 방법이다.**

```java
public class Singleton {
 
    private Singleton(){}
    
    private static class SingletonHelper{
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance(){
        return SingletonHelper.INSTANCE;
    }
}
```

private inner static class를 두어 싱글톤 인스턴스를 갖게 한다.

이때 2번이나 3번 방식과의 차이점이라면 SingletonHelper 클래스는 Singleton 클래스가 Load 될 때에도 Load 되지 않다가 getInstance()가 호출됐을 때 비로소 JVM 메모리에 로드되고, 인스턴스를 생성하게 된다.

아울러 synchronized를 사용하지 않기 때문에 5번에서 문제가 되었던 성능 저하 또한 해결된다.

### 7. Enum Singleton

앞서 2~6번에서 살펴본 싱글톤 방식은 사실 완전히 안전할 수 없다. 왜냐하면 Java의 Reflection을 통해서 싱글톤을 파괴할 수 있기 때문이다.

이에 Java 계의 거장 Joshua Bloch는 Enum으로 싱글톤을 구현하는 방법을 제안하였다.

```java
public enum EnumSingleton {

    INSTANCE;
    
    public static void doSomething(){
        //do something
    }
}
```

그러나 이 방법 또한 2, 3번과 같이 사용하지 않았을 경우의 메모리 문제를 해결하지 못한 것과 유연성이 떨어진다는 면에서의 한계를 지니고 있다.

### 8. 결론

- 싱글톤 방식에 가장 중요하다고 생각하는 것은 싱글톤 객체는 상태를 유지하게 설계하면 안된다는 것이다.
- 싱글톤 방식에서는 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기때문에 싱글톤을 설계할때는 항상 `무상태`로 설계를 하도록 하자

### 참고

- https://www.baeldung.com/java-singleton-double-checked-locking
- https://ttl-blog.tistory.com/89
