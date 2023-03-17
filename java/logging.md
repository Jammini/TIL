# Logging에 대해 알아보자

### 목차

1. [로깅이란?](#1-로깅이란)
2. [어떻게 기록할까?](#2-어떻게-기록할까)
3. [로깅프레임워크](#3-로깅프레임워크)
4. [결론](#4-결론)

### 1. 로깅이란?

- 소프트웨어가 실행 중에 발생하는 이벤트를 기록하는 행위.
- 소프트웨어 동작 상태를 파악하고 문제가 발생했을때 동작을 파악하고 소프트웨어 문제를 찾아내고 해결하기 위해 디자인 됨.

### 2. 어떻게 기록할까?

- 앞서 1번은 운영환경에서 절대로 남기면 안된다. 우린 1번이 아닌 2번에 대해 알아본다. 1번을 사용하면 안되는 이유는 [여기](https://github.com/Jammini/TIL/blob/master/java/System.out.println-%EC%A0%88%EB%8C%80-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%EB%A7%88%EB%9D%BC.md)를 살펴보자.
    1. System.out.println(”Hello”);
    2. 로깅 프레임워크

### 3. 로깅프레임워크

- 스프린부트를 사용하면 스프링부트 로깅 라이브러리(`spring-boot-starter-logging`)이 포함된다.
- 기본적으로 아래와 같은 로깅 라이브러리를 사용한다.

<img width="516" alt="image" src="https://user-images.githubusercontent.com/59176149/225929417-4353ab15-9736-4a8e-b43c-6c7c1fbab082.png">

- 로그 라이브러리는 Logback, Log4j, Log4j2 등등 수 많은 라이브러리가 있는데, 그것을 통합해서 인터페이스로 제공하는 것이 바로 SLF4J 이다.
- SLF4J는 인터페이스고 그 구현체로 Logback 같은 로그 라이브러리를 선택하면 된다.
- 실무에서는 스프링부트가 기본으로 제공하는 Logback을 대부분 사용한다.

**로그선언**

- private logger log = LoggerFactory.getLogger(getClass());
- private static final Logger log = LoggerFactory.getLogger(xxx.class);
- @Slf4j : 롬복사용

**로그레벨설정**

| 레벨 | 설명 |
| --- | --- |
| TRACE | Debug 환경에서 버그를 해결하기 위해 사용. 더 자세함. |
| DEBUG | Info 레벨 보다 더 자세한 정보가 필요한 경우. |
| INFO | 명확한 의도가 있는 에러, 요구 사항에 따라 시스템 동작을 보여줄때. |
| WARN | 에러가 될 수 있는 잠재적 가능성이 있는 경우. |
| ERROR | 의도하지 않은 에러가 바랭한 경우. 프로그램이 종료되진 않음. |
| FATAL | 매우 심각한 에러, 프로그램이 종료되는 경우가 많음. |
- 위의 표에서 위로 갈 수록 레벨이 높다고 표현하며 로그 레벨을 설정해서 출력을 할 수있다.
- 주로 개발 서버는 DEBUG 설정
    - DEBUG 밑에 레벨인 INFO, WARN, ERROR, FATAL이 출력
- 주로 운영서버는 INFO 설정
    - INFO 밑에 레벨인 WARN, ERROR, FATAL이 출력

**올바른 로그 사용법**

- `log.debug("data = " + data)`
    - 로그 출력 레벨을 INFO로 설정해도 해당 코드에 있는 “data=”+data가 실제 실행이 되버린다. 결과적으로 문자 더하기 연산이 발생함.
- `log.debug("data = {}", data)`
    - 로그 출력레벨을 INFO로 설정하면 아무일도 발생하지 않는다. 따라서 앞과 같은 의미없는 연산이 발생하지 않는다.

### 4. 결론

- 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.
- 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영서버에서는 출력하지 않는 등 로그를 상황에 맞게 조절할 수 있다.
    - 즉, 애플리케이션 코드를 변경하지 않고 properties와 같은 설정만으로 변경 할 수 있다는 것.
- 시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등 로그를 별도의 위치에 남길 수 있다. 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할 하는 것도 가능하다.
- 성능도 일반 System.out보다 좋다. (내부 버퍼링, 멀티쓰레드 등등) 그래서 실무에서는 꼭 로그를 사용해야 한다.

### 참고

- [https://github.com/Jammini/TIL/blob/master/java/System.out.println-%EC%A0%88%EB%8C%80-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%EB%A7%88%EB%9D%BC.md](https://github.com/Jammini/TIL/blob/master/java/System.out.println-%EC%A0%88%EB%8C%80-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%EB%A7%88%EB%9D%BC.md)
- [https://www.youtube.com/watch?v=1MD5xbwznlI](https://www.youtube.com/watch?v=1MD5xbwznlI)
- [https://www.inflearn.com/course/스프링-mvc-1/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)
