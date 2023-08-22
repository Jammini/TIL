# 데코레이터패턴(****Decorator Pattern****)이란?

### 목차

1. [데코레이터패턴(Decorator Pattern)이란?](#1-데코레이터패턴decorator-pattern이란)
2. [데코레이터 패턴 구조](#2-데코레이터-패턴-구조)
3. [패턴 적용하기](#3-패턴-적용하기)
4. [장점과 단점](#4-장점과-단점)
5. [자바와 스프링에서 찾아보는 패턴](#5-자바와-스프링에서-찾아보는-패턴)
6. [프록시 패턴과 데코레이터 패턴](#6-프록시-패턴과-데코레이터-패턴)

## 1. 데코레이터패턴(Decorator Pattern)이란?

데코레이터 패턴(Decorator Pattern)은 대상 객체에 대한 기능 확장이나 변경이 필요할때 객체의 결합을 통해 서브클래싱 대신 쓸수 있는 유연한 대안 구조 패턴이다.

데코레이터 패턴을 이용하면 필요한 추가 기능의 조합을 **런타임에서 동적**으로 생성할 수 있다.

## 2. 데코레이터 패턴 구조

<img width="700" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/9550ac6e-0414-431e-a8d3-4a8ca87ea89e">

- Component (Interface) : 원본 객체와 장식된 객체 모두를 묶는 역할
- ConcreteComponent : 원본 객체 (데코레이팅 할 객체)
- Decorator : 추상화된 장식자 클래스
    - 원본 객체를 합성(composition)한 wrappee 필드와 인터페이스의 구현 메소드를 가지고 있다
- ConcreteDecorator : 구체적인 장식자 클래스
    - 부모 클래스가 감싸고 있는 하나의 Component를 호출하면서 호출 전/후로 부가적인 로직을 추가할 수 있다.

## 3. 패턴 적용하기

위 그림과 같이 아래 코드를 데코레이터 패턴을 적용하면 다음과 같다.

- App

```java
public class App {

    private static boolean enabledSpamFilter = true;

    private static boolean enabledTrimming = true;

    public static void main(String[] args) {
        CommentService commentService = new DefaultCommentService();

        if (enabledSpamFilter) {
            commentService = new SpamFilteringCommentDecorator(commentService);
        }

        if (enabledTrimming) {
            commentService = new TrimmingCommentDecorator(commentService);
        }

        Client client = new Client(commentService);
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
    }
}
```

- Client

```java
public class Client {
    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    public void writeComment(String comment) {
        commentService.addComment(comment);
    }
}
```

- Component(CommentService)

```java
public interface CommentService {
    void addComment(String comment);
}
```

- ConcreteComponent(DefaultCommentService)

```java
public class DefaultCommentService implements CommentService {
    @Override
    public void addComment(String comment) {
        System.out.println(comment);
    }
}
```

- Decorator(CommentDecorator)

```java
public class CommentDecorator implements CommentService {
    private CommentService commentService;

    public CommentDecorator(CommentService commentService) {
        this.commentService = commentService;
    }

    @Override
    public void addComment(String comment) {
        commentService.addComment(comment);
    }
}
```

- ConcreteDecorator1(TrimmingCommentDecorator)

```java
public class TrimmingCommentDecorator extends CommentDecorator {

    public TrimmingCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("...", "");
    }
}
```

- ConcreteDecorator2(SpamFilteringCommentDecorator)

```java
public class SpamFilteringCommentDecorator extends CommentDecorator {

    public SpamFilteringCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        if (isNotSpam(comment)) {
            super.addComment(comment);
        }
    }

    private boolean isNotSpam(String comment) {
        return !comment.contains("http");
    }
}
```

## 4. 장점과 단점

- 장점
    - 데코레이터를 사용하면 서브클래스를 만들때보다 훨씬 더 유연하게 기능을 확장할 수 있다.
    - 객체를 여러 데코레이터로 래핑하여 여러 동작을 결합할 수 있다.
    - 컴파일 타임이 아닌 런타임에 동적으로 기능을 변경할 수 있다.
    - 각 장식자 클래스마다 고유의 책임을 가져 단일 책임 원칙(SRP)을 준수
    - 클라이언트 코드 수정없이 기능 확장이 필요하면 장식자 클래스를 추가하면 되니 개방 폐쇄 원칙(OCP)을 준수
    - 구현체가 아닌 인터페이스를 바라봄으로써 의존 역전 원칙(DIP)준수
- 단점
    - 만일 장식자 일부를 제거하고 싶다면, Wrapper 스택에서 특정 wrapper를 제거하는 것은 어렵다.
    - 데코레이터를 조합하는 초기 생성코드가 보기 안좋을 수 있다. new A(new B(new C(new D())))
    - 어느 장식자를 먼저 데코레이팅 하느냐에 따라 데코레이터 스택 순서가 결정지게 되는데, 만일 순서에 의존하지 않는 방식으로 데코레이터를 구현하기는 어렵다.

## 5. 자바와 스프링에서 찾아보는 패턴

- InputStream, OutputStream, Reader, Writer의 생성자를 활용한 랩퍼
    - java.io.InputStream, OutputStream, Reader, Writer의 모든 하위 클래스에 동일한 유형의 인스턴스를 사용하는 생성자
- java.util.Collections가 제공하는 메소드들 활용한 랩퍼
    - java.util.Collections의 checkedXXX(), synchronizedXXX(), unmodifiableXXX() 메서드들
- javax.servlet.http.HttpServletRequestWrapper 그리고 HttpServletResponseWrapper

## 6. 프록시 패턴과 데코레이터 패턴

- 두 패턴 모두 프록시를 사용하는 방법이다.
- 하지만 GOF 디자인 패턴에서는 이 둘을 의도(Intent)에 따라서 구분한다.
    - [프록시 패턴](https://github.com/Jammini/TIL/blob/master/java/proxypattern.md) : 접근 제어가 목적
    - 데코레이터 패턴 : 새로운 기능 추가가 목적

### 참고

- [https://inpa.tistory.com/entry/GOF-💠-데코레이터Decorator-패턴-제대로-배워보자#패턴_장점](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0Decorator-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90#%ED%8C%A8%ED%84%B4_%EC%9E%A5%EC%A0%90)
- [https://www.inflearn.com/course/디자인-패턴/dashboard](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
