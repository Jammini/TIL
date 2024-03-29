# 서블릿(Servelet)이란?

### 목차

1. [서블릿(Servlet)이란?](#1-서블릿servlet이란)
2. [서블릿 특징](#2-서블릿-특징)
3. [서블릿 동작방식](#3-서블릿-동작방식)
4. [서블릿 컨테이너](#4-서블릿-컨테이너)
5. [동시요청 - 멀티쓰레드](#5-동시요청---멀티쓰레드)
6. [서블릿 생명주기(Servlet LifeCycle)](#6-서블릿-생명주기servlet-lifecycle)
7. [결론](#7-결론)

### 1. 서블릿(Servlet)이란?

- **서블릿은 동적인 페이지를 만들기 위해 사용되는 웹 애플리케이션 프로그램**

<img width="697" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4f826b99-d85b-41f0-8edd-56e630df8e05">

위와 같이 서버에서 처리해야 하는 업무들이 많지만 서블릿을 지원하는 WAS를 사용하면 초록색 박스와 같은 의미있는 비즈니스 로직을 제외한 모든일들을 대신 처리해준다.

즉, 개발자는 의미있는 비즈니스 로직에만 집중하여 개발 할 수 있게 도와준다.

### 2. 서블릿 특징

<img width="703" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/5c7aaaea-dc69-4943-bf2a-260092f60b63">

- urlPatterns(/hello) 의 URL이 호출되면 서블릿 코드가 실행된다.
- HTTP 요청 정보를 쉽게 사용할 수 있는 HttpServletRequest를 제공한다.
- HTTP 응답 정보를 쉽게 사용할 수 있는 HttpServletResponse를 제공한다.
- 개발자는 HTTP 스펙을 매우 편리하게 사용 할 수 있다.

### 3. 서블릿 동작방식

<img width="689" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/939c5a23-20aa-4e49-a9d0-5c20385b5e12">

1. 웹 브라우저에서 localhost:8080/hello를 요청한다.
2. HTTP 요청 메시지를 기반으로 request, response 객체를 만든다. 
3. 우리가 만든 helloServlet을 실행시켜준다.
4. 종료되면 2번에서 만든 response 객체 정보로 HTTP응답을 생성한다.
5. 웹브라우저에 내용을 전달한다.

### 4. 서블릿 컨테이너

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 한다.
- 서블릿 컨테이너는 **서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리**
- 서블릿 객체는 **싱글톤으로 관리**
    - 고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율
    - 최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용
    - 모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근**공유 변수 사용 주의**
    - 공유 변수 사용 주의
    - JSP도 서블릿으로 변환 되어서 사용
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원
****

### 5. 동시요청 - 멀티쓰레드

- 만약 한 요청을 처리하기전에 다른 요청이 들어오면 **동시 요청을 처리하기 위해 멀티쓰레드로 사용**한다
- 멀티 쓰레드의 경우 **주의해야할 점**이 많은데, 요청마다 쓰레드를 생성한다면 리소스가 허용할 때 까지 처리가 가능하지만 쓰레드의 생성 비용은 비싸고, 컨텍스트 스위칭 오버헤드가 발생하게 된다. 만약 제한을 두지않는다면 하드웨어의 임계점을 넘으면 서버가 죽을 수 있다.

<img width="696" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/73eae7da-1a4a-4917-bc30-55277fcc8000">

그래서 위와 같이 쓰레드를 미리 만들어 **쓰레드 풀에 보관하고 관리**해야 한다. 쓰레드가 필요하면 이미 생성되어 있는 쓰레드를 꺼내 사용하고 종료하면 다시 쓰레드 풀에 반납하는 형태이다.

### 6. 서블릿 생명주기(Servlet LifeCycle)

1. 먼저 WAS는 클라이언트로부터 서블릿 요청을 받으면 해당 서블릿이 메모리에 있는지 확인한다.

2-1. **(만약 해당 서블릿이 처음실행되어 메모리에 없다면)** 서블릿 클래스를 메모리에 올리고 init() 메소드와 service() 메소드를 실행한다.

2-2. **(만약 해당 서블릿이 메모리에 있다면)** service() 메소드를 실행한다.

3. WAS가 종료되거나 웹 어플리케이션이 갱신되어 서블릿 종료 요청이 있을 경우 destroy() 메소드를 실행한다.

<img width="719" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/ca6c4035-d906-4413-a77a-8f3ebb48e21f">

```jsx
@WebServlet("/ServletLifeCycle")
public class ServletLifeCycle extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    public ServletLifeCycle() {
        super();
        System.out.println("ServletLifeCycle 생성자 실행");
    }

	public void init(ServletConfig config) throws ServletException {
		System.out.println("init() 실행");
	}

	protected void service(HttpServletRequest request, HttpServletResponse response)
    						throws ServletException, IOException {
		System.out.println("service() 실행");
		super.service(request, response);
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
    						throws ServletException, IOException {
		System.out.println("doGet() 실행");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) 
    						throws ServletException, IOException {
		System.out.println("doPost() 실행");
	}
	
	public void destroy() {
		System.out.println("destroy() 실행");
	}
}
```

<img width="716" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/7b03c558-a43e-4875-94b6-00f813d606b8">

### 7. 결론

- 멀티쓰레드에 대한 부분은 서블릿 컨테이너(WAS)가 처리하여 개발자는 멀티쓰레드 관련 코드를 신경쓰지 않아도 된다는 장점이 생긴다.
- 개발자는 마치 싱글 쓰레드 프로그래밍을 하듯이 편리하게 소스코드를 개발할 수 있지만, 어쨋든 멀티쓰레드 환경이므로 싱글톤 객체에서의 공유변수들은 항상 조심해서 사용해야 한다.

### 참고

- https://dding9code.tistory.com/41
- https://www.youtube.com/watch?v=2pBsXI01J6M
- https://kadosholy.tistory.com/47