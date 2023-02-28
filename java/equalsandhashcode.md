# equals() 를  오버라이드 하면 hashCode()도 같이 재정의 해야한다?

### 목차

1. [equals()란?](#1-equals란)
2. [hashCode()란?](#2-hashcode란)
3. [equals()만 재정의할 경우](#3-equals만-재정의할-경우)
4. [hashCode 재정의](#4-hashcode-재정의)
5. [equals() 재정의 한다면 hashCode()도 재정의 해주자!](#5-equals-재정의-한다면-hashcode도-재정의-해주자)
6. [참고](#참고)


자바의 모든 클래스는 Object 클래스를 상속 받는다.

Object 클래스에는 `equals()`와 `hashCode()`라는 메소드가 선언되어 있다.


### 1. equals()란?

- 참조값이 같은지 판단한다. 즉 동일 객체인지를 확인하는 기능.

```java
public class Object {
		public boolean equals(Object obj) {
        return (this == obj);
	  }
}
```

### 2. hashCode()란?

- 객체의 해시코드를 반환하는 메서드
- Object클래스의 hashCode()는 객체의 주소를 int로 변환해서 반환

```java
public class Object {
		public native int hashCode();
}
```

### 3. equals()만 재정의할 경우

- Member클래스에 equals만 재정의를 한 코드이다.

```java
public class Member {
    private final String name;

    public Member(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        Member member = (Member) o;
        return Objects.equals(name, member.name);
    }
}
```

- equals()를 재정의 하지 않으면 Object에 있는 equals()를 사용해 동일 객체인지 판단하는 함수를 사용하여 `false` 가 출력 된다.

```java
public static void main(String[] args){
    Member member1 = new Member("Jammini");
    Member member2 = new Member("Jammini");
    System.out.println(member1.equals(member2)); // true 출력
}
```

- equals()를 재정의 했기 때문에 Member객체의 내부의 값인 name을 비교하여 동등하다고 판단하여 `true` 가 출력 출력된다.

**즉, 우리가 원하는 결과를 얻이 위해선 객체의 내부의 값을 비교할 수 있도록 equals()를 재정의 해야한다**

```java
public static void main(String[] args) {
    List<Member> members = new ArrayList<>();
    members.add(new Member("Jammini"));
    members.add(new Member("Jammini"));
    System.out.println("members.size() = " + members.size()); // 2
}
```

- 중복값이 허용되는 List에 Member 객체를 만들어 넣었다. 출력결과는 2가 나오는 것을 볼 수 있다.

```java
public static void main(String[] args) {
    Set<Member> members= new HashSet<>();
    members.add(new Member("Jammini"));
    members.add(new Member("Jammini"));
    System.out.println("members.size() = " + members.size()); // 2

}
```

- 이번에는 중복 값을 허용하지 않는 Set으로 변경하였으나 동일하게 2가 출력 되는 것을 볼 수 있다.

즉, hashCode를 equals와 함게 재정의하지 않으면 코드가 에상과 다르게 작동하는 위와 같은 문제가 발생한다. 좀 더 정확히는 hash 값을 사용하는 Collection(HashSet, HashMap, HashTable)을 사용할 때 문제가 발생한다.

hash 값을 사용하는 객체가 논리적으로 같은지 비교할 때 아래 그림과 같은 과정을 거친다.

<img width="676" alt="image" src="https://user-images.githubusercontent.com/59176149/221889627-dd4e8b89-3b2c-4cd4-bc46-8fa6daec082c.png">


- 위의 코드에서 중복을 허용하지 않는 Set에서 Size가 2가 나왔던 것은 Member 클래스에 hashCode가 재정의 되어 있지 않아 Object에 있는 hashCode() 가 사용되어 다른 객체로 판단을 한 것이다.

### 4. hashCode 재정의

```java
public class Member {
    private final String name;

    public Member(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        Member member = (Member) o;
        return Objects.equals(name, member.name);
    }

		@Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```

- 이와 같이 hashCode()를 재정의 해주고 중복을 허용했던 Set의 Size를 다시 구해보자

```java
public static void main(String[] args) {
    Set<Member> members= new HashSet<>();
    members.add(new Member("Jammini"));
    members.add(new Member("Jammini"));
    System.out.println("members.size() = " + members.size()); // 1
}
```

- equals()만 재정의 해주었을 경우 출력 값이 2가 나왔었으나 우리가 원하는 결과대로 중복을 허용하지 않는 Set의 결과인 1을 출력한걸 확인 할 수 있었다.

### 5. equals() 재정의 한다면 hashCode()도 재정의 해주자!

hash 값을 사용하는 Collection을 사용한다면 우리가 원하는 결과 값을 얻기 위해 반드시 재정의 해줘야한다는 것을 코드와 함께 살펴보았다. 

IDE에서 제공하는 generate를 이용해 쉽게 재정의하거나 Lombok을 사용해서 간단하게 재정의 해주도록 하자!

### 참고

- Effective Java 3rd Edition - Item 11.

- [https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/](https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/)

