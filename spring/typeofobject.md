# 객체의 종류

### Object

아래 User 라는 객체가 있고 다음과 같다.

```java
class User {
	// nullable: false, updatable: false
	private long id;
	// nullable: false, updateble: false
	private String username;
	// nullable: false, updatable: true
	private String password;
	// nullable: false, updatable: false
	private String email;

	public void changePassword(String before, String after) {
		// ...
	}
}
```

### VO

UserInfo에서 멤버 변수 들을 final로 선언해 상태를 변경할 수 없게 만들어 보면 다음과 같다.

이런 객체를 VO(Value Object)라고 부르며 변할 수 없는 객체(=값 객체)라고 할 수 있다.

```java
class UserInfo {
	private final long id;
	private final String username;
	private final String email;

	public UserInfo(long id, String username, String email) {
		this.id = id;
		this.username = username;
		this.email = email;
	}
}
```

VO는 불변해야 하며, 이는 동일하게 생성된 두 VO는 영원히 동일한 상태임을 유지되어야 한다는 것을 의미한다. 또한 VO는 잘못된 상태로는 만들어 질 수 없다. 따라서 인스턴스화 된 VO는 **항상 유효**하므로 버그를 줄이는데에도 유용하다.

항상유효하다는 것은 VO는 값을 만들때 값이 유효한지 항상 체크해 줘야 한다.

```java
class UserInfo {
	private final long id;
	private final String username;
	private final String email;

	public UserInfo(long id, String username, String email) {
		assert id > 0;
		assert StringUtils.isNotEmpty(username);
		assert EmailValidator.isValid(email);
		this.id = id;
		this.username = username;
		this.email = email;
	}
}
```

### * 생성자의 역할

생성자는 가급적 2가지 역할만 해야한다.

1. 값 검증
2. 값 할당

### DTO (Data Transfer Object)

메소드 간, 클래스 간, 프로세스 간 데이터를 주고받을때 쓰는 객체

DTO는 상태를 보호하지 않으면 모든 속성을 노출하므로 획득자와 설정자가 필요없다. 이는 public 속성으로 충분하는 뜻이다. - [오브젝트 디자인 스타일 가이드 팀의 생산성을 높이는 고품질 객체지향 코드 작성법](https://www.yes24.com/Product/Goods/91167539) 참조

사실상 아래 두개의 클래스는 같은거다. 즉, 데이터를 전송할때 이런 변수를 파라미터로 하나하나 나열하기 힘드니까 하나로 묶어서 전송하는 객체라고 생각하자.

```java
class UserCreateRequest {
	public String username;
	public String password;
	public String email;
}
```

```java
class UserCreateRequest {
	private String username;
	private String password;
	private String email;
}
```

### Entity

요청이나 응답 값을 전달하는 클래스로 사용해서는 안된다.

데이터베이스와 매핑되어있는 핵심 클래스이다.

유일한 식별자가 있고, 수명 주기가 있으며, 쓰기 모델 저장소에 저장함으로써 지속성을 가지며 나중에 저장소에 블러올 수 있다.

### 결론

- 객체의 종류에는 위와 같이 3종류만 있는 것이 아니며, 완벽한 분류는 어렵다.
- 분류보다는 이런 고민을 해보자
    - 어떤 값을 불변으로 만들 것인가?
    - 어떤 인터페이스를 노출할 것인가?
    