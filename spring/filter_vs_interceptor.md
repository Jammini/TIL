# 필터(filter) vs 인터셉터(interceptor)

### 목차

1. [개요](#1-개요)
2. [필터(filter)란?](#2-필터filter란)
    
    2-1. [필터 흐름](#2-1-필터-흐름)
    
    2-2. [필터 체인](#2-2-필터-체인)
    
    2-3. [필터 인터페이스](#2-3-필터-인터페이스)

    2-4. [필터 예외처리](#2-4-필터-예외처리)
    
3. [인터셉터(interceptor)란?](#3-인터셉터interceptor란)
    
    3-1. [인터셉터 흐름](#3-1-인터셉터-흐름)
    
    3-2. [인터셉터 체인](#3-2-인터셉터-체인)
    
    3-3. [인터셉터 인터페이스](#3-3-인터셉터-인터페이스)

    3-4. [인터셉터 예외처리](#3-4-인터셉터-예외처리)
    
4. [필터 vs 인터셉터](#4-필터-vs-인터셉터)
    
    4-1. [관리되는 컨테이너 영역](#4-1-관리되는-컨테이너-영역)
    
    4-2. [스프링의 예외 처리 여부](#4-2-스프링의-예외-처리-여부)
    
    4-3. [Request/Response 객체 조작 가능 여부](#4-3-requestresponse-객체-조작-가능-여부)
    
    4-4. [필터(Filter)와 인터셉터(Interceptor)의 용도 및 예시](#4-4-필터filter와-인터셉터interceptor의-용도-및-예시)
    
5. [결론](#5-결론)

## 1. 개요

- 서블릿 필터와 스프링 인터셉터는 웹과 같이 공통 관심사항을 효과적으로 해결할 수 있는 기술이다.
- 필터는 서블릿이 제공하는 기술이고, 인터셉터는 스프링 MVC가 제공하는 기술이다.
- 둘다 웹과 관련된 공통 관심 사항을 처리하지만 적용되는 순서와 범위 그리고 사용범위는 다르다.

## 2. 필터(filter)란?

- J2EE 표준 스펙 기능으로 디스패처 서블릿(Dispatcher Servlet)에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가작업을 처리할 수 있는 기능을 제공한다
- 즉, 스프링 컨테이너가 아닌 톰캣과 같은 웹 컨테이너에 의해 관리가 되는 것이고, 스프링 범위 밖에서 처리되는 것이다.

### 2-1. 필터 흐름

<img width="725" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3525e97a-289d-462b-9e7f-1e49e8817ba8">

- 필터에서 적절하지 않은 요청이라고 판단하면 서블릿으로 넘기지 않고 걸러 낼 수도 있다.

### 2-2. 필터 체인

- HTTP 요청 → WAS → 필터1 → 필터2 → 필터3 → 서블릿 → 컨트롤러
- 필터는 체인으로 구성되는데, 중간에 필터를 자유롭게 추가할 수 있다. 예를 들어서 로그를 남기는 필터를 먼저 적용하고, 그 다음에 로그인 여부를 체크하는 필터를 만들 수 있다.

### 2-3. 필터 인터페이스

```java
public interface Filter {
      public default void init(FilterConfig filterConfig) throws ServletException
  {}
      public void doFilter(ServletRequest request, ServletResponse response,
              FilterChain chain) throws IOException, ServletException;
      public default void destroy() {}
}
```

- 필터 인터페이스를 구현하고 등록하면 서블릿 컨테이너가 필터를 싱글톤 객체로 생성하고, 관리한다.
1. init()
    - 필터 초기화 메서드, 서블릿 컨테이너가 생성될 때 호출된다.
2. doFilter()
    - 고객의 요청이 올 때 마다 해당 메서드가 호출된다. 필터의 로직을 구현하면 된다.
3. destroy()
    - 필터 종료 메서드, 서블릿 컨테이너가 종료될 때 호출된다.

### 2-4. 필터 예외처리

오류가 발생하면 오류 페이지를 출력하기 위해 WAS 내부에서 다시 한번 호출이 발생한다.

서브릿, 인터셉터도 모두 다시 호출된다. 그런데 로그인 인증 체크 같은 경우를 생각해보면, 이미 한번 필터나, 인터셉터에서 로그인 체크를 완료했다. 따라서 서버 내부에서 오류페이지를 호출한다고 해서 해당 필터나 인터셉트가 한번 더 호출되는 것은 비효울적이다.

결국, 클라이언트로부터 발생한 요청이 정상인지 오퓨페이지를 출력하기 위한 요청인지 구분할 수 있어야 한다.

서블릿은 이런 문제를 해결하기 위해 `DispatcherType`이라는 추가 정보를 제공한다.

```java
public enum DispatcherType {
	FORWARD, // 서블릿에서 다른 서블릿이나 JSP를 호출할때 RequestDispatcher.forward(request, response);
	INCLUDE, // 서블릿에서 다른 서블릿이나 JSP의 결과를 포함할때 RequestDispatcher.include(request, response);
	REQUEST, // 클라이언트 요청
	ASYNC, // 서블릿 비동기 호출
	ERROR // 오류 요청
}
```

기본 값은 `DispatcherType=REQUEST`이다

고객이 처음 정상적으로 요청하면 `DispatcherType=REQUEST`이다. 

예외가 발생하여 오류페이지를 요청하게 되면 `DispatcherType=ERROR`로 나오게 된다.

## 3. 인터셉터(interceptor)란?

- Spring이 제공하는 기술로써 Dispatcher Servlet이 Controller를 호출하기 전 / 후에 인터셉터가 끼어들어 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공한다.
- 웹 컨테이너에서 동작하는 필터와 달리 인터셉터는 스프링 컨텍스트에서 동작한다.
- 디스패처 서블릿이 핸들러 매핑을 통해 컨트롤러를 찾도록 요청하는데, 그 결과로 실행 체인(HandlerExecutionChain)을 돌려준다. 여기서 1개 이상의 인터셉터가 등록되어 있다면 순차적으로 인터셉터들을 거쳐 컨트롤러가 실행되도록 하고, 인터셉터가 없다면 바로 컨트롤러를 실행한다.
- 실제로 Interceptor가 직접 Controller로 요청을 위임하는 것은 아니다

### 3-1. 인터셉터 흐름

<img width="720" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a3b4a1fc-78c8-4024-ad3e-ceac93e6a035">

- 스프링 인터셉터는 스프링 MVC가 제공하는 기능이기 때문에 디스패처 서블릿 이후에 등장하게 된다.
- 스프링 인터셉터에도 URL 패턴을 적용할 수 있는데, 서블릿 URL 패턴과는 다르고, 매우 정밀하게 설정할 수 있다.

### 3-2. 인터셉터 체인

- HTTP 요청 → WAS → 필터 → 서블릿 → 인터셉터1 → 인터셉터2 → 컨트롤러
- 스프링 인터셉터는 체인으로 구성되는데, 중간에 인터셉터를 자유롭게 추가할 수 있다. 예를 들어서 로그를 남기는 인터셉터를 먼저 적용하고, 그 다음에 로그인 여부를 체크하는 인터셉터를 만들 수 있다.
- 지금까지 내용을 보면 서블릿 필터와 호출 되는 순서만 다르고, 제공하는 기능은 비슷해 보인다. 앞으로 설명하겠지만, 스프링 인터셉터는 서블릿 필터보다 편리하고, 더 정교하고 다양한 기능을 지원한다.

### 3-3. 인터셉터 인터페이스

```java
public interface HandlerInterceptor {
 
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		return true;
	}
 
	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}
 
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}
}
```

1. preHandle()
    - Controller가 호출되기 전에 실행된다.
    - 컨트롤러 이전에 처리해야 하는 전처리 작업이나 요청 정보를 가공하거나 추가하는 경우에 사용할 수 있다.
2. postHandle()
    - Controller가 호출된 후에 실행된다. ( View 렌더링 전)
    - 컨트롤러 이후에 처리해야 하는 후처리 작업이 있을 때 사용할 수 있다. 이 메소드는 컨트롤러가 반환하는 ModelAndView 타입의 정보가 제공되는데, 최근에는 JSON 형태로 데이터를 제공하는 RestAPI 기반의 컨트롤러(@RestController)를 만들면서 자주 사용되지 않는다.
3. afterCompletion()
    - 모든 뷰에서 최종 결과를 생성하는 일을 포함해 모든 작업이 완료된 후에 실행된다. (View 렌더링 후)
    - 요청 처리 중에 사용한 리소스를 반환할 때 사용할 수 있다.

### 3-4. 인터셉터 예외처리

인터셉터도 필터와 마찬가지로 예외처리가 날 경우 중복 호출되는 부분을 막는 것이 필요하다.

인터셉터는 필터와 다르게 `DispatcherType`이 존재하지 않는다. 대신, 인터셉터는 excludePathPatterns를 이용해 에러와 관련된 페이지를 이용해 걸러낸다.

즉, 인터셉터는 경로 정보로 중복 호출을 제거한다. (`excludePathPatterns(”URL”)`)

## 4. 필터 vs 인터셉터

### 4-1. 관리되는 컨테이너 영역

<img width="696" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/fa2b4ac8-b6fe-4532-b840-e23b05d39aa6">

- 위 그림에서 보이듯이 필터와 인터셉터는 관리되는 영역이 다르다.
- 필터는 스프링 이전의 서블릿 영역에서 관리되지만, 인터셉터는 스프링 영역에서 관리되는 영역이기 때문에 필터는 스프링이 처리해주는 내용들을 적용 받을 수 없다.
- 이로 인한 차이로 발생하는 대표적인 예시가 스프링에 의한 예외처리가 되지 않는다는 것이다.

### 4-2. 스프링의 예외 처리 여부

- 일반적으로 스프링을 사용한다면 ControllerAdvice와 ExceptionHandler를 이용한 [예외처리 기능](https://mangkyu.tistory.com/205)을 주로 사용한다. 예를 들어 원하는 멤버를 찾지 못하여 로직에서 MemberNotFoundException을 던졌다면 404 Status로 응답을 반환하길 원할 것이다. 그리고 이를 위해 우리는 다음과 같은 예외 처리기를 구현하여 활용할 것이다. 이를 통해 예외가 서블릿까지 전달되지 않고 처리된다.

```java
@RestControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(MemberNotFoundException.class)
    public ResponseEntity<Object> handleMyException(MemberNotFoundException e) {
        return ResponseEntity.notFound()
            .build();
    }

    ...
}
```

- 하지만 앞서 설명하였듯 필터는 스프링 앞의 서블릿 영역에서 관리되기 때문에 스프링의 지원을 받을 수 없다. 그래서 만약 필터에서  MemberNotFoundException이 던져졌다면, 에러가 처리되지 않고 서블릿까지 전달된다. 서블릿은 예외가 핸들링 되기를 기대했지만, 예외가 그대로 올라와서 예상치 못한 Exception을 만난 상황이다. 따라서 내부에 문제가 있다고 판단하여 500 Status로 응답을 반환한다. 이를 해결하려면 필터에서 다음과 같이 응답(Response) 객체에 예외 처리가 필요하다.

```java
public class MyFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse servletResponse = (HttpServletResponse) response;
        servletResponse.setStatus(HttpServletResponse.SC_NOT_FOUND);
        servletResponse.getWriter().print("Member Not Found");
    }
}
```

### 4-3. Request/Response 객체 조작 가능 여부

- 필터는 Request와 Response를 조작할 수 있지만 인터셉터는 조작할 수 없다. 여기서 조작한다는 것은 내부 상태를 변경한다는 것이 아니라 다른 객체로 바꿔친다는 의미이다. 이는 필터와 인터셉터의 코드를 보면 바로 알 수 있다.
- 필터가 다음 필터를 호출하기 위해서는 필터 체이닝(다음 필터 호출)을 해주어야 한다. 그리고 이때 Request/Response 객체를 넘겨주므로 우리가 원하는 Request/Response 객체를 넣어줄 수 있다. NPE가 나겠지만 null로도 넣어줄 수 있는 것이다.

```java
public MyFilter implements Filter {

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
// 개발자가 다른 request와 response를 넣어줄 수 있음
        chain.doFilter(new MockHttpServletRequest(), new MockHttpServletResponse());
    }

}
```

- 인터셉터는 처리 과정이 필터와 다르다. 디스패처 서블릿이 여러 인터셉터 목록을 가지고 있고, for문으로 순차적으로 실행시킨다. 그리고 true를 반환하면 다음 인터셉터가 실행되거나 컨트롤러로 요청이 전달되며, false가 반환되면 요청이 중단된다. 그러므로 우리가 다른 Request/Response 객체를 넘겨줄 수 없다. 그리고 이러한 부분이 필터와 확실히 다른 점이다.

```java
public class MyInterceptor implements HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
// Request/Response를 교체할 수 없고 boolean 값만 반환할 수 있다.return true;
    }

}
```

### 4-4. 필터(Filter)와 인터셉터(Interceptor)의 용도 및 예시

### **필터(Filter)의 용도 및 예시**

1. 공통된 보안 및 인증/인가 관련 작업
2. 모든 요청에 대한 로깅 또는 감사
3. 이미지/데이터 압축 및 문자열 인코딩
4. Spring과 분리되어야 하는 기능
- 필터에서는 기본적으로 스프링과 무관하게 전역적으로 처리해야 하는 작업들을 처리할 수 있다.
- 대표적으로 보안 공통 작업이 있다. 필터는 인터셉터보다 앞단에서 동작하므로 전역적으로 해야하는 보안 검사(XSS 방어 등)를 하여 올바른 요청이 아닐 경우 차단을 할 수 있다. 그러면 스프링 컨테이너까지 요청이 전달되지 못하고 차단되므로 안정성을 더욱 높일 수 있다.
- 또한 필터는 이미지나 데이터의 압축이나 문자열 인코딩과 같이 웹 애플리케이션에 전반적으로 사용되는 기능을 구현하기에 적당하다. Filter는 다음 체인으로 넘기는 ServletRequest/ServletResponse 객체를 조작할 수 있다는 점에서 Interceptor보다 훨씬 강력한 기술이다.

### **인터셉터(Interceptor)의 용도 및 예시**

1. 세부적인 보안 및 인증/인가 공통 작업
2. API 호출에 대한 로깅 또는 감사
3. Controller로 넘겨주는 정보(데이터)의 가공
- 인터셉터에서는 클라이언트의 요청과 관련되어 전역적으로 처리해야 하는 작업들을 처리할 수 있다.
- 대표적으로 세부적으로 적용해야 하는 인증이나 인가와 같이 클라이언트 요청과 관련된 작업 등이 있다. 예를 들어 특정 그룹의 사용자는 어떤 기능을 사용하지 못하는 경우가 있는데, 이러한 작업들은 컨트롤러로 넘어가기 전에 검사해야 하므로 인터셉터가 처리하기에 적합하다.
- 인터셉터는 필터와 다르게 HttpServletRequest나 HttpServletResponse 등과 같은 객체를 제공받으므로 객체 자체를 조작할 수는 없다. 대신 해당 객체가 내부적으로 갖는 값은 조작할 수 있으므로 컨트롤러로 넘겨주기 위한 정보를 가공하기에 용이하다. 예를 들어 사용자의 ID를 기반으로 조회한 사용자 정보를 HttpServletRequest에 넣어줄 수 있다.
- 그 외에도 우리는 다양한 목적으로 API 호출에 대한 정보들을 기록해야 할 수 있다. 이러한 경우에 HttpServletRequest나 HttpServletResponse를 제공해주는 인터셉터는 클라이언트의 IP나 요청 정보들을 포함해 기록하기에 용이하다.

대표적으로 필터(Filter)를 인증과 인가에 사용하는 도구로는 SpringSecurity가 있다. SpringSecurity의 특징 중 하나는 Spring MVC에 종속적이지 않다는 것인데, 이러한 이유로는 필터 기반으로 인증/인가 처리를 하기 때문이다.

## 5. 결론

<img width="697" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/2d85a272-ea04-4d06-aed0-a9c79906cfbc">

- 필터와 인터셉터에 대해 간단하게 정리하였다. 추가로 AOP에 관해서도 필터와 인터셉터를 비교를 많이 하기에 내용을 정리하여 추가로 기재하도록 하자.

### 참고

- https://dev-coco.tistory.com/173
- https://mangkyu.tistory.com/173