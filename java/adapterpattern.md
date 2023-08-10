# 어댑터패턴(Adapter Pattern)이란?

### 목차

1. [어댑터 패턴이란?](#1-어댑터-패턴이란)
    
    1-1. [어댑터 패턴이 해결하는 문제](#1-1-어댑터-패턴이-해결하는-문제)
    
    1-2. [문제 해결 예제 1](#1-2-문제-해결-예제-1)
    
    1-3. [문제 해결 예제 2](#1-3-문제-해결-예제-2)
    
2. [패턴 적용하기](#2-패턴-적용하기)
3. [장점과단점](#3-장점과단점)
4. [자바와 스프링에서 찾아보는 패턴](#4-자바와-스프링에서-찾아보는-패턴)
    
    4-1. [Collection 예제](#4-1-collection-예제)
    
    4-2. [java.io 패키지 예제](#4-2-javaio-패키지-예제)
    
    4-3. [스프링의 HandlerAdapter](#4-3-스프링의-handleradapter)
    

## 1. 어댑터 패턴이란?

일상에서 흔히 볼 수 있는 콘센트라 보면 된다.

우리가 110V 콘센트를 220V에 꽂거나, 반대로 220V 콘센트를 110V에 꽂으려고 할 때, 그 사이에 쓰는 어댑터(돼지코)를 비유하면 가장 쉽게 이해 할 수 있다.

소프트웨어적으로 보면, 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴을 말한다.

즉, 클라이언트가 사용하는 인터페이스를 따르지 않는 기존 코드를 재사용할 수 있게 해준다.

<img width="582" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a9ed18cd-f4cb-4863-b1fc-53de46cc7d2c">

### 1-1. 어댑터 패턴이 해결하는 문제

객체지향 어댑터는 아래와 같이 인터페이스를 클라이언트에서 요구하는 형태의 인터페이스에 적응 시켜주는 역할을 하는 것이다.

외부 라이브러리 클래스를 사용하고 싶은데, 클래스의 인터페이스가 다른 코드와 호환되지 않을 때 이를 해결해 줄 수 있다.

<img width="591" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/ee5d3d88-b198-4282-bd13-3b465dd75e2b"

### 1-2. 문제 해결 예제 1

- 외부 라이브러리에 전세계 날씨와 관련된 정보를 XML 형태로 반환하는 클래스가 있다.
    - 국가, 날짜, 시간 등을 입력하면 날씨를 반환해준다.
- 회사의 프론트엔드 표준은 JSON 데이터를 기준으로 하고 있다.
- 이 때, 외부 라이브러리 데이터를 JSON 형태로 변환해주는 어댑터를 생성하여 이용할 수 있다.

### 1-3. 문제 해결 예제 2

- 고객사 서비스에서는 통합회원관리 API 서비스를 제공한다.
    - 회원관리 API 에 회원 정보를 넣어 가입, 로그인, 탈퇴 등을 할 수 있다.
- 애플리케이션을 만들 때, 회원 관련 API는 따로 만들기 번거로워서 고객사 서비스를 이용하기로 한다.
    - 그런데 자사 서비스는 익명 서비스라 회원정보 조회 API 를 호출했을 경우에도 회원 이름, ID 등은 철저하게 비밀리에 관리해야 한다는 요구사항이 있다.
    - 고객사 API 를 쓰되, 회원 정보 조회 API 를 사용하는 암호화 회원 정보 조회 어댑터를 만들어 자사 서비스에 맞게 해당 API 를 변환할 수 있을 것이다.

## 2. 패턴 적용하기

어댑터 패턴을 적용하는 간단한 예제를 살펴보자

UserDetailsService와 UserDetails이 `Target`인터페이스에 해당.

```java
public interface UserDetails {
    String getUsername();
    String getPassword();
}
```

```java
public interface UserDetailsService {
    UserDetails loadUser(String username);
}
```

LoginHandler는 `Client`에 해당한다.

```java
public class LoginHandler {
    UserDetailsService userDetailsService;

    public LoginHandler(UserDetailsService userDetailsService) {
        this.userDetailsService = userDetailsService;
    }

    public String login(String username, String password) {
        UserDetails userDetails = userDetailsService.loadUser(username);
        if (userDetails.getPassword().equals(password)) {
            return userDetails.getUsername();
        } else {
            throw new IllegalArgumentException();
        }
    }
}
```

아래 Account는 AccountService 클래스는 `Adaptee`에 해당한다.

```java
@Getter
@Setter
public class Account {
    private String name;
    private String password;
    private String email;
}
```

```java
public class AccountService {
    public Account findAccountByUsername(String username) {
        Account account = new Account();
        account.setName(username);
        account.setPassword(username);
        account.setEmail(username);
        return account;
    }

    public void createNewAccount(Account account) {

    }

    public void updateAccount(Account account) {

    }
}
```

중간에 우리는 AccountUserDetails, AccountUserDetailsService클래스와 같이 `Adapter`를 만들어 

```java
public class AccountUserDetails implements UserDetails {
    private Account account;

    public AccountUserDetails(Account account) {
        this.account = account;
    }

    @Override
    public String getUsername() {
        return account.getName();
    }

    @Override
    public String getPassword() {
        return account.getPassword();
    }
}
```

```java
public class AccountUserDetailsService implements UserDetailsService {
    private AccountService accountService;

    public AccountUserDetailsService(AccountService accountService) {
        this.accountService = accountService;
    }

    @Override
    public UserDetails loadUser(String username) {
        return new AccountUserDetails(accountService.findAccountByUsername(username));
    }
}
```

`Client` 는 Adapter에 해당하는 AccountUserDetailsService와 AccountUserDetails을 만들면서 기존에 코드는 손대지 않고 편리하게 이용할 수 있게 되었다.

아래 코드를 실행하면 정상적으로 작동 하는 것을 확인할 수 있다.

```java
public class App {
    public static void main(String[] args) {
        AccountService accountService = new AccountService();
        UserDetailsService userDetailsService = new AccountUserDetailsService(accountService);
        LoginHandler loginHandler = new LoginHandler(userDetailsService);
        String login = loginHandler.login("jammini", "jammini");
        System.out.println(login);
    }
}
```

간략하게 적용된 다이어그램을 살펴보면 아래와 같다.

<img width="617" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/05c45d0a-d98d-4b86-b272-edd83dcb6d7d">

## 3. 장점과단점

- 장점
    - 기존 코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다.
        - 기존 코드를 손상시키지 않는 것은 객체지향 원칙 중 `OCP(개방/폐쇄 원칙)` 에 해당한다.
    - 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하여 관리할 수 있다.
        - 역할에 맞게 코드를 분리하는 것은 객체지향 원칙 중 `SRP(단일 책임 원칙)`에 해당한다.
- 단점
    - 다수의 새로운 인터페이스와 클래스를 도입해야 하므로 구조가 복잡해진다.
    - 때로는 서비스 클래스를 변경하는 것이 더 간단할 때도 있다.

## 4. 자바와 스프링에서 찾아보는 패턴

### 4-1. Collection 예제

```java
List<String> strings = Arrays.asList("a", "b", "c");
Enumeration<String> enumeration = Collections.enumeration(strings);
ArrayList<String> list = Collections.list(enumeration);
```

- 클라이언트가 간단한 문자열만 인자로 넘겨도 `Collections` 클래스를 생성할 수 있도록 도와준다.
- 둘째 줄의 경우, `strings` 가 `Adaptee` 의 역할이며, `Collections` 가 `Adapter` 에 해당하는 역할이고, `Enumeration` 이 `Target` 에 해당하는 역할이다.

### 4-2. java.io 패키지 예제

```java
try(InputStream is = new FileInputStream("input.txt");
    InputStreamReader isr = new InputStreamReader(is);
    BufferedReader reader = new BufferedReader(isr)) {
    while(reader.ready()) {
        System.out.println(reader.readLine());
    }
} catch (IOException e) {
    throw new RuntimeException(e);
}
```

- `txt` 파일을 읽어 (`File`) `InputStream` 으로 만든 후 `InputStreamReader` 로 만들고 `BufferedReader` 로 만든 후 코드에서 활용하고 있다.
    - `File` -> `InputStream` -> `InputStreamReader` -> `BufferedReader`
    - 어댑터를 통해 무려 3단 변신을 한다.

### 4-3. 스프링의 HandlerAdapter

- 우리가 작성하는 다양한 형태의 핸들러 코드를 스프링MVC가 실행할 수 있는 형태로 변환해주는 어댑터용 인터페이스이다.
- 우리가 여태까지 봤던 형태와 다르게 인터페이스 형태로 `Adapter` 를 제공해주고 있다.
    - 디자인 패턴은 딱 정해진 것이 아니라 보는 시각에 따라 다른 패턴으로 보일 수 있다.
- `HandlerAdapter` 인터페이스를 구현하면, `DispatcherServlet` 필드에 `HandlerAdapter` 로 등록되어 이용될 수 있게 해준다.
    - 스프링의 요청 처리 동작을 편의성 좋은 객체들을 통해 내 마음대로 확장할 수 있도록 도와준다.

### 참고

- https://jusungpark.tistory.com/22
- https://jake-seo-dev.tistory.com/379
- [https://www.inflearn.com/course/디자인-패턴/dashboard](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)