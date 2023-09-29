# **ExceptionHandler와 ControllerAdvice를 알아보자**

### 목차

1. [개요](#1-개요)
2. [스프링의 기본적인 예외 처리](#2-스프링의-기본적인-예외-처리)
3. [@ExceptionHandler](#3-exceptionhandler)
4. [@ControllerAdvice](#4-controlleradvice)
5. [결론](#5-결론)

## 1. 개요

예외 처리를 하는 방법은 다양하나 일반적으로 간단하게 예외를 핸들링하기 편한 방법으로 try / catch문을 자주 사용한다.

스프링을 사용한 웹 애플리케이션에서 적용할 수 있는 예외 처리 방법인 `@ExceptionHandler`와 `@ControllerAdvice`에 대해 알아보자.

## 2. 스프링의 기본적인 예외 처리

```java
@RestController
public class SimpleController {

    @GetMapping(path = "/errorExample")
    public String invokeError() {
        throw new IllegalArgumentException();
    }
}
```

url로 `/errorExample` 요청을 보내면 스프링이 제공하는 기본 에러 페이지가 표시되는걸 볼 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/fd81e7de-ac90-4359-851d-169f7827a770/1e611850-de0d-44ae-a4a1-ca3eff3e937b/Untitled.png)

`BasicErrorController` 는 요청의 Accept 헤더 값이 text/html일 때, 예외가 발생하면 `/error` 라는 경로로 재요청을 보내준다.

상세하게 예외의 내용을 응답에 담고 싶으면 `BasicErrorController`를 상속한 `@Controller` 클래스를 만들어 `errorHtml()` 메서드와 `error()` 메서드를 재정의해주면 된다. 이 방법은 url을 알면 누구나 마음대로 error페이지를 접근하게 되는데 이러한 단점을 `@ExceptionHandler`를 사용해 해결할 수 있다.

## 3. @ExceptionHandler

스프링은 API 예외 처리 문제를 해결하기 위해 `@ExceptionHandler` 라는 애노테이션을 사용하는 매우 편리한 예외 처리 기능을 제공하는데, 이것이 바로 `ExceptionHandlerExceptionResolver` 이다.

스프링은 `ExceptionHandlerExceptionResolver` 를 기본으로 제공하고, 기본으로 제공하는 `ExceptionResolver` 중에 우선순위도 가장 높다. 실무에서 API 예외 처리는 대부분 이 기능을 사용한다.

```java
@RestController
public class SimpleController {

    // ...

    @ExceptionHandler(value = IllegalArgumentException.class)
    public ResponseEntity<String> invokeError(IllegalArgumentException e) {
         ...
				return new ResponseEntity<>("error Message", HttpStatus.BAD_REQUEST);
    }
}
```

**@ExceptionHandler 예외 처리 방법**

@ExceptionHandler 애노테이션을 선언하고, 해당 컨트롤러에서 처리하고 싶은 예외를 지정해주면 된다.
해당 컨트롤러에서 예외가 발생하면 이 메서드가 호출된다. 참고로 지정한 예외 또는 그 예외의 자식클래스는 모두 잡을 수 있다.

**우선순위**

스프링의 우선순위는 항상 자세한 것이 우선권을 가진다. 예를 들어서 부모, 자식 클래스가 있고 다음과 같이 예외가 처리된다.

```java
@ExceptionHandler(부모예외.class)
public String 부모예외처리()(부모예외 e) {}
@ExceptionHandler(자식예외.class)
public String 자식예외처리()(자식예외 e) {}
```

`@ExceptionHandler` 에 지정한 부모 클래스는 자식 클래스까지 처리할 수 있다. 

자식예외가 발생할 경우.

부모예외처리(), 자식예외처리() 둘다 호출 대상이지만 우선권은 자식예외가 호출된다.

즉, 부모,자식예외가 둘 다 호출대상이어도 더 자세한 것이 우선순위를 가진다.

**다양한 예외**

```java
@ExceptionHandler({AException.class, BException.class})
public String ex(Exception e) {
    log.info("exception e", e);
}
```

위와 같이 다양한 예외를 한번에 처리할 수도 있다.

**동작방식**

```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
@ExceptionHandler(IllegalArgumentException.class)
public ErrorResult illegalExHandle(IllegalArgumentException e) {
	log.error("[exceptionHandle] ex", e);
	return new ErrorResult("BAD", e.getMessage());
}
```

1. 컨트롤러를 호출한 결과 IllegalArgumentException 예외가 컨트롤러 밖으로 던져진다. 
2. 예외가 발생했으로 ExceptionResolver 가 작동한다. 가장 우선순위가 높은 ExceptionHandlerExceptionResolver 가 실행된다.
3. ExceptionHandlerExceptionResolver 는 해당 컨트롤러에 IllegalArgumentException 을 처리할 수 있는 @ExceptionHandler 가 있는지 확인한다.
4. illegalExHandle() 를 실행한다. @RestController 이므로 illegalExHandle() 에도 @ResponseBody 가 적용된다. 따라서 HTTP 컨버터가 사용되고, 응답이 다음과 같은 JSON으로 반환된다.
5. @ResponseStatus(HttpStatus.BAD_REQUEST) 를 지정했으므로 HTTP 상태 코드 400으로 응답한다.

`@ExceptionHandler`는 코드를 작성한 컨트롤러에서만 발생하는 예외만 처리된다.

만약 같은 예외에 대해 여러 컨트롤러에서 같은 처리를 하고 싶다면 컨트롤러마다 같은 메서드를 작성해 주어야만 한다.

즉, 코드의 중복이 발생할 수밖에 없다.

## 4. @ControllerAdvice

`@ExceptionHandler` 를 사용해서 예외를 깔끔하게 처리할 수 있게 되었지만, 정상 코드와 예외 처리 코드가 하나의 컨트롤러에 섞여 있다. `@ControllerAdvice` 또는 `@RestControllerAdvice` 를 사용하면 둘을 분리할 수 있다.

이를 통해 코드의 중복을 해결할 수 있고 하나의 클래스 내에서 정상 동작 시 호출되는 코드와 예외를 처리하는 코드를 분리할 수 있다.

다음은 `@ControllerAdvice` 와 `@RestConrollerAdvice` 의 구현의 일부이다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@ControllerAdvice
@ResponseBody
public @interface RestControllerAdvice {
    ...
}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface ControllerAdvice {
    ...
}
```

`@Component`어노테이션을 붙이면 Component Scan 과정을 거쳐 Bean으로 등록된다.

즉, 이 어노테이션을 사용하는 `ControllerAdvice` 또한 Bean으로 관리된다는 것을 알 수 있다.

또한, `@RestControllerAdvice`는 `@ResponseBody` 어노테이션이 붙어있으므로 응답을 Json으로 처리한다는 것을 알 수 있다.

**대상 컨트롤러 지정 방법**

```java
// Target all Controllers annotated with @RestController
// 특정 애노테이션이 있는 컨트롤러를 지정할 수 있다
@ControllerAdvice(annotations = RestController.class)
public class ExampleAdvice1 {}
// Target all Controllers within specific packages
// 특정 패키지를 직접 지정할 수도 있다.  
@ControllerAdvice("org.example.controllers")
public class ExampleAdvice2 {}
// Target all Controllers assignable to specific classes
// 특정 클래스를 지정할 수도 있다. 대상 컨트롤러 지정을 생략하면 모든 컨트롤러에 적용된다.
@ControllerAdvice(assignableTypes = {ControllerInterface.class,
AbstractController.class})
public class ExampleAdvice3 {}
```

https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-controller-advice (스프링 공식 문서 참고)

### 4-1. 내부 동작방식

인터페이스인 `@ControllerAdvice`와 `@RestConrollerAdvice`어떻게 도작할까?

**스프링의 동작방식**

<img width="704" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a35a0120-6099-44ad-afb6-6579b4d58705">

1. 클라이언트는 URL 주소를 통해 서버에 요청을 보낸다.
2. 클라이언트의 요청이 Dispatcher Servlet으로 들어오고,
3. Dispatcher Servlet은 요청과 매핑되는 컨트롤러를 찾아서
    - 컨트롤러들에 대한 정보를 갖고 있는 HandlerMapping 에서 요청받은 URL, Http Method(GET/POST 등), 리턴 타입, 매개변수 정보 등을 이용하여 적합한 컨트롤러를 검색한다.
4. 해당 컨트롤러에게 처리를 요청한다.

**Dispatcher Servlet 코드**

시작과 끝은 DispatcherServlet 이다.

doDispatch 부분에서 processDispatchResult로 들어간다.

예외가 없으면(exception == null) 정상적으로 처리한다.  이번에 살펴볼 것은 예외 처리다.

```java
public class DispatcherServlet extends FrameworkServlet {
   protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
     ....
     ....
     processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
     ....
     .... 
  }

   private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
			@Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
			@Nullable Exception exception) throws Exception {
       ....
	   if (exception != null) {
			if (exception instanceof ModelAndViewDefiningException) {
				...
			}
			else {
				Object handler = (mappedHandler != null ? mappedHandler.getHandler() : null);
				mv = processHandlerException(request, response, handler, exception);
				errorView = (mv != null);
			}
		}
      ....
   }

  protected ModelAndView processHandlerException(HttpServletRequest request, HttpServletResponse response,
			@Nullable Object handler, Exception ex) throws Exception {

		...

		// Check registered HandlerExceptionResolvers...
		ModelAndView exMv = null;
		if (this.handlerExceptionResolvers != null) {
			for (HandlerExceptionResolver resolver : this.handlerExceptionResolvers) {
				exMv = resolver.resolveException(request, response, handler, ex);
				if (exMv != null) {
					break;
				}
			}
		}
		....
        ....
		throw ex;
	}
}
```

1. Dispatcher Servlet이 동작을 하다가
2. 에러가 발생(exception != null) 하면,
3. processHandlerExcpetion 으로 들어가서,
4. HandlerExceptionResolver를 받아와 resolveException을 하게 되는데
5. HandlerExceptionResolver에는 @ControllerAdvice 빈이 등록되어 주입됨

**흐름**

<img width="705" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/10b5d38f-0c4d-4b87-8174-e9196a9032ad">

사용자의 요청이 들어오면 디스패처 서블릿은 요청에 알맞은 컨트롤러를 찾아 처리를 요청하는데,

이때 exception이 발생하게 되면 (등록된 ControllerAdivce 빈이 주입된) HandlerExceptionResolver가 익셉션을 잡게 되고,

ControllerAdvice 빈에서 익셉션을 잡아 작성한 ExceptionHandler 클래스 내부의 ExceptionHandler로 넘어가는데,

ExceptionHandler는 명시된 익셉션 클래스에 속하는 익셉션을 잡는다.

- 발생한 익셉션의 근본 원인 익셉션이 특정 익셉션의 인스턴스인지에 따라, 각기 다른 에러 메세지, status를 넣어주도록 작성할 수 있다.
- 에러 메세지와 status는 별도의 열거형으로 분리하여 관리하면 체계적인 익셉션핸들링이 가능하다.

## 5. 결론

- 스프링에서는 `@ExceptionHandler` `@ControllerAdvice`를 조합하면 예외처리를 깔끔하게 해결할 수 있다.
- 코드가 간결해지고, 에러 처리 코드의 위치를 사용자가 유연하게 관리할 수 있다는 장점이 있다.

### 참고

- https://www.baeldung.com/spring-controllers
- https://tecoble.techcourse.co.kr/post/2023-05-03-ExceptionHandler-ControllerAdvice/
