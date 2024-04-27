# Lombok(롬복)은 어떻게 동작하는 걸까?

- Lombok
    - @Getter, @Setter, @Builder 등의 애노테이션 프로세서를 제공하여 표준적으로 작성해야 할 코드를 개발자 대신 생성해주는 라이브러리

```java
public class Member {
	private String name;
	private int age;
}
```

- Member의 변수는 private 으로 선언 되어 있기 때문에, 이 멤버 변수의 값을 가져오거라 수정하려면 getter, setter를 생성해줘야 함.

```java
public class Memebr {
  private String name;
	private int age;

  //setter
  public void setName(String name) {
    this.name = name;
  }
	public void SetAge(int age) {
		this.age = age;
	}

  //getter
  public String getName() {
    return this.name;
  }
	public int getAge() {
		return this.age;
	}
}
```

- 멤버 변수가 늘어날 때마다 개발자가 getter와 setter를 반복적으로 생성해줘야 함.
    - 이때 클래스에 @Getter, @Setter를 사용하여 이런 보일러플레이트를 줄여줌.

```java
@Getter @Setter
public class Member {
	private String name;
	private int age;
}
```

롬복이 어떻게 구현되어 있길래 애노테이테이션의 추가로 이런 작업을 알아서 해주는 것인가?

- 롬복의 동작원리
    - 컴파일 시점에 애노테이션 프로세서를 사용하여 소스코드의 [AST(Abstract Syntax Tree)](https://javaparser.org/inspecting-an-ast/)를 조작
    - https://www.happykoo.net/@happykoo/posts/256
    

논란거리

- 공개된 API가 아닌 컴파일러 내부 클래스를 사용하여 기존 소스 코드를 조작한다.
- 특히 이클립스의 경우엔 java agent를 사용하여 컴파일러 클래스까지 조작하여 사용한다. 해당 클래스들 역시 공개된 API가 아니다보니 버전 호환성에 문제가 생길 수 있고 언제라도 그런 문제가 발생해도 이상하지 않다.
- 그럼에도 불구하고 엄청난 편리함 때문에 널리 쓰이고 있으며 대안이 몇가지 있지만 롬복의 모든 기능과 편의성을 대체하진 못하는 현실이다.
    - AutoValue
        - https://github.com/google/auto/blob/master/value/userguide/index.md
    - Immutables
        - [https://immutables.github.io](https://immutables.github.io/)
- http://jnb.ociweb.com/jnb/jnbJan2010.html#controversy