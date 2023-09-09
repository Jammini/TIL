# 싱글톤패턴(Singletone Pattern)이란?

### 목차

1. [싱글톤 패턴이란?](#1-싱글톤-패턴이란)
2. [싱글톤 패턴을 가장 단순히 구현하는 방법](#2-싱글톤-패턴을-가장-단순히-구현하는-방법)
3. [멀티 쓰레드 환경에서 안전하게 구현하는 방법](#3-멀티-쓰레드-환경에서-안전하게-구현하는-방법)
4. [싱글톤 패턴 구현 방법을 깨트리는 방법](#4-싱글톤-패턴-구현-방법을-깨트리는-방법)
5. [자바와 스프링에서 찾아보는 패턴](#5-자바와-스프링에서-찾아보는-패턴)
6. [결론](#6-결론)

### 1. 싱글톤 패턴이란?

인스턴스를 오직 한개만 제공하는 클래스

- 시스템 런타임, 환경 세팅에 대한 정보 등, 인스턴스가 여러개 일 때 문제가 생길 수 있는 경우가 있다. 인스턴스를 오직 한개만 만들어 제공하는 클래스가 필요하다.

### 2. 싱글톤 패턴을 가장 단순히 구현하는 방법

아래와 같이 Settings라는 class가 있다고 하자

```java
public class Settings  {
	
}
```

싱글톤 패턴을 사용하려면 절대 new를 사용해서는 안된다.

```java
Settings settings1 = new Settings();
Settings settings2 = new Settings();
System.out.println(settings1 != settings2); // true
```

그렇다면 new 사용하지 못하게 하기 위해 Settings 클래스에 private한 생성자를 만들어 주면 된다.

즉, 인스턴스를 만들지 못하기 때문에 해달 클래스안에서 글로벌하게 접근할 수 있는 인스턴스를 만들어 줘야 한다.

```java
public class Settings  {

	private static Settings instance;

	private Settings() {}
	
	public static Settings getInstance() {
		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}
}
```

아래 처럼 settings1과 settings2가 같은 인스턴스라는 것을 확인할 수 있다.

```java
Settings settings1 = Settings.getInstance();
Settings settings2 = Settings.getInstance();
System.out.println(settings1 == settings2); // true
```

아래 코드는 가장 간단하게 싱글톤 패턴을 구현할 수 있지만 멀티쓰레드환경에서 쓰레드 세이프 하지 않다.

```java
public static Settings getInstance() {
		if (instance == null) {
			// 먼저 한개의 쓰레드가 여기까지 진입하여 instance를 할당 받기 전 다른 쓰레드도 이 if문 안으로 들어 올 수 있다.
			instance = new Settings(); 
		}
		return instance;
	}
```

만약, 2개의 쓰레드가 존재한다고 할 때 먼저 한개의 쓰레드가 여기까지 진입하여 instance를 할당 받기 전 다른 쓰레드도 이 if문 안으로 들어 올 수 있다. 그렇게 되면 각각의 쓰레드에 대한 인스턴스는 달라지게 된다.

그래서 여러 방법으로 멀티쓰레드 환경에서 안전하게 구현하는 방법에 대해서 알아보려고 한다.

### 3. 멀티 쓰레드 환경에서 안전하게 구현하는 방법

- https://github.com/Jammini/TIL/blob/master/java/singleton.md

### 4. 싱글톤 패턴 구현 방법을 깨트리는 방법

1. **리플렉션 사용하기**

```java
Settings settings1 = Settings.getInstance();

Constructor<Settings> constructor = Settings.class.getDeclaredConstructor();
constructor.setAccessible(true);
Settings settings2 = constructor = constructor.newInstance();

System.out.println(settings1 == settings2); // false
```

1-1. **리플렉션 대응 방안**

https://github.com/Jammini/TIL/blob/master/java/singleton.md#7-enum-singleton

1. **직렬화 & 역직렬화 사용하기**

직렬화란 오브젝트를 파일 형태로 디스크에 저장하는 것이다. 반대로 다시 읽어들이는 것을 역직렬화라고 한다.

기본적으로**Serializable** 인터페이스를 구현한 클래스의 인스턴스들은 직렬화와 역직렬화에 사용할 수 있다. 객체를 파일로 저장했다가 다시 로딩할 수 있다.

```java
Settings settings1 = Settings.getInstance();
 
try(ObjectOutput output = new ObjectOutputStream(new FileOutputStream("settings.obj"))){
    output.writeObject(settings1);
}

Settings settings2 = null;

try(ObjectInput input = new ObjectInputStream(new FileInputStream("settings.obj"))){
    settings2 = (Settings) input.readObject();
}

System.out.println(settings1 == settings2); // false
```

역직렬화를 할 때 반드시 생성자를 통해 다시 한번 인스턴스를 만들기 때문에 직렬화에 사용한 인스턴스와는 전혀 다른 인스턴스가 된다.

2-1. **역직렬화 대응 방안**

역직렬화의 대응 방안으로 **readResolve**가 있다. readResolve를 정의하면 readObject를 통해 만들어진 인스턴스 대신 readResolve가 반환하는 인스턴스가 역직렬화의 결과물이 된다.

```java
public class Settings implements Serializable {
    private Settings() {
    }
 
    private static class SettingsHolder {
        private static final Settings INSTANCE = new Settings();
    }
 
    public static Settings getInstance() {
        return SettingsHolder.INSTANCE;
    }
 
    protected Object readResolve(){
        return SettingsHolder.INSTANCE;
    }
}
```

### 5. 자바와 스프링에서 찾아보는 패턴

- 스프링에서 빈의 스코프 중에 하나로 싱글톤 스코프.
- 자바 java.lang.Runtime
- 다른 디자인 패턴(빌더, 퍼사드, 추상 팩토리 등) 구현체의 일부로 쓰이기도 한다.
1. Runtime

```java
Runtime runtime = Runtime.getRuntime();
System.out.println("runtime = " + runtime.maxMemory());
System.out.println("runtime.freeMemory() = " + runtime.freeMemory());
```

- 이 경우 new로 선언이 안됨
- 이 런타임은 자바 어플리케이션 실행하는 환경에 대한것
    - 실행환경의 메모리 정보등 출력 가능
1. 스프링의 경우

```java
public clas SpringsExample{
  public static void main(String[] args){
		ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
 
    string hello = applicationContext.applicationContext.getBean("hello", String.class);
    string hello1 =applicationContext.applicationContext.getBean("hello", String.class);
	
    System.out.println(hello == hello1)
    	
  }
}

// 다른 클래스
public class SpringConfig{
	@Bean
  public String hello(){
		return hello;
  }
}
```

- 두개의 같은 인스턴스가 나옴
- 싱글톤 패턴이라고 부르기 보다는 싱글톤 스코프라고 함
    - 이것은 유일한 인스턴스로 관리하는것, 엄밀히 말하면 싱글톤은 아니다.
- 다른것이지만 유일한 객체가 필요한 경우 저런식으로 등록해서 사용 한다.

### 6. 결론

- 지난 번에 [Java 에서 싱글톤패턴 구현방식](https://github.com/Jammini/TIL/blob/master/java/singleton.md)에 대해 알아 보았으나, 중요한 내용이니 만큼 그 외에 단순히 싱글톤을 만들었을때의 문제점과 싱글톤 구현을 깨트리는 방법 등을 학습하였다.

### 참고

- [https://www.inflearn.com/course/디자인-패턴/dashboard](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
