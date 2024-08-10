# System.out.println 메소드는 실무에서 절대 사용하지마라.

### 목차

1. [개요](#1-개요)
2. [System.out.println 무엇인가?](#2-systemoutprintln-무엇인가)
3. [왜 사용해서는 안되는가?](#3-왜-사용해서는-안되는가)
4. [로그를 남기면 안되는 것인가?](#4-로그를-남기면-안되는-것인가)
5. [결론](#5-결론)

### 1. 개요

프로그래밍을 처음 접하면 `System.out.println(”Hello World”);`같이 콘솔에 출력하는 것을 배울 것이다.

코딩을 시작하는 단계에서는 검정바탕에 'Hello World'를 모르는 개발자는 없을 것이다.

우리는 기능 구현을 하면 원하는 값이 제대로 나오는지 확인하기 위해 출력을 하는 경우가 대부분이다.

그러나, 실무에서 'System.out.println을 **`절대`** 사용을 하면 안된다.'

그 이유를 아래에서 차근차근 알아보자.

### 2. System.out.println 무엇인가?

- System.out.println은 Java개발할때 디버깅 용도로 콘솔에 출력하는 메소드 중 하나다.
- System은 java.lang 패키지의 내장된 최종 클래스다.
- out은 System 클래스의 정적 멤버 필드이며 PrintStream이다.
- println은 PrintStream 클래스의 메서드이고, 표준 콘솔에 전달된 인자와 줄 바꿈을 출력한다.

### 3. 왜 사용해서는 안되는가?

1. 성능 이슈
    
    이유는 크게 2가지로 **블로킹 I/O와 멀티쓰레드에서 락이 발생하기 때문이다.**
    
    `System.out.println`이 끝날때까지 아무 일을 실행할 수 없고 대기해야 하기에 성능을 저하시킬 수 있다.

2. 로그 레벨 관리가 어렵다
    
    로그 레벨을 지정할 수 없기 때문에 디버깅 용도로 사용되는 경우, 어떤 로그 레벨로 출력되는지 확인하기 어렵다. 
    
    로그 레벨이 제대로 관리되지 않으면, 프로덕션 환경에서도 불필요한 디버깅 정보가 출력되어 시스템의 안정성과 보안에 문제가 생길 수 있다.
    
3. 유지보수성 저하
    
    출력 메시지가 코드에 하드코딩되어 있으면 나중에 메시지를 수정하거나 삭제하는 등의 변경 작업이 어려울 수 있다.
    

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("hello World");
    }
}
```

System.out.println("hello World");를 살펴보자

<img width="627" alt="image" src="https://user-images.githubusercontent.com/59176149/225928091-b4693cba-ffcf-42ce-a4f8-3987a38a8a88.png">

- 위 처럼 println 메서드에는 synchronized block을 볼 수 있다. 이는 동시에 여러 스레드가 해당 블록이나 메서드에 접근하지 못하게 Lock을 걸어 버린다.
- 즉, System.out.println 콘솔 출력으로 인해 성능 저하를 초래할 수 있는 것이다.

어느 사이트의 화면 선택시 `System.out.println`를 개선한 총 소요시간을 나타낸 비교 표를 살펴보자

|  | 응답 시간 | 개선율 |
| --- | --- | --- |
| 변경 전 | 1,242ms | - |
| 변경 1 | 893ms | 39% |
| 변경 2 | 504ms | 146% |
- 변경 1은 로거를 사용하면서 로그 사용여부를 false했을 경우.
- 변경 2는 모든 로거를 주석 처리하고 System.out.println 을 제거한 경우.
- 각각 변경 1은 39%의 개선가 변경 2는 146%가 개선된 것을 볼 수 있다.
- 따라서, 시스템 로그를 프린트하면 반드시 성능에 영향을 주게 된다.

```
개선율이란 튜닝 전과 후의 차이를 수치로 나타낸 것이다. 다음의 공식으로 구한다.
(튜닝 전 응답 속도 - 튜닝 후 응답 속도) * 100 / 튜닝 후 응답 속도 = 개선율(%)
```

### 4. 로그를 남기면 안되는 것인가?

그렇다면 로그를 남기고 싶다면 뭘써야할까?

- 로거(Logger)를 사용하여 로그를 남기는 습관을 가지자.
- 로컬에서 `System.out.println` 사용해도 무방하겠지만 배포할때도 실수를 방지하기 위해 로거를 사용하는게 좋다.
- 흔히, 현업에서 로그를 남길때 로깅프레임워크로 logback이나 log4j를 많이 사용한다.

### 5. 결론

- 운영 시스템에서는 System.out.println을 절대 사용해서는 안된다.
- 로그를 남기고 싶다면 로깅프레임워크를 사용하여 적절한 로그 레벨을 지정하여 출력하는 것이 좋다.
- 로깅프레임워크에 대해 더 알아보려면 [여기](https://github.com/Jammini/TIL/blob/master/java/logging.md)로 넘어와서 보자.

### 참고
- [https://github.com/Jammini/TIL/blob/master/java/logging.md](https://github.com/Jammini/TIL/blob/master/java/logging.md)
- [https://examples.javacodegeeks.com/java-development/core-java/lang/system/out/logging-system-println-results-log-file-example/](https://examples.javacodegeeks.com/java-development/core-java/lang/system/out/logging-system-println-results-log-file-example/)
- [https://code-run.tistory.com/8#2.1. Reference](https://code-run.tistory.com/8#2.1.%20Reference%C2%A0)
- [https://hudi.blog/do-not-use-system-out-println-for-logging/](https://hudi.blog/do-not-use-system-out-println-for-logging/)
- [https://velog.io/@jsj3282/10.-로그는-반드시-필요한-내용만-찍자](https://velog.io/@jsj3282/10.-%EB%A1%9C%EA%B7%B8%EB%8A%94-%EB%B0%98%EB%93%9C%EC%8B%9C-%ED%95%84%EC%9A%94%ED%95%9C-%EB%82%B4%EC%9A%A9%EB%A7%8C-%EC%B0%8D%EC%9E%90)
