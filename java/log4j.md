# Log4j란?

### 목차

1. [개요](#1-개요)
2. [Log4j란?](#2-log4j란)
3. [Log4j 특징](#3-log4j의-특징)
4. [Log4j 구조](#4-log4j-구조)
5. [결론](#5-결론)

### 1. 개요

평사시에 롬복라이브러리를 자주 사용하는데 로그를 남기고 싶을때 Log4j 어노테이션을 이용하여 자주 사용하였다.

하지만 로그를 남기는 용도로만 사용하였을뿐 자세히 Log4J에 대해 알지 못하여 정리를 해보려고 한다.

### 2. Log4j란?

자바 애플리케이션에서 로깅(logging)을 구현하기 위한 오픈 소스 라이브러리다.

로깅은 애플리케이션의 동작 및 이벤트를 기록하는 것을 의미하며, 애플리케이션의 디버깅, 모니터링, 문제 해결, 보안 등 다양한 목적으로 사용된다. log4j는 이러한 로깅 작업을 효율적으로 처리할 수 있는 도구를 제공한다.

### 3. Log4j의 특징

- log4j는 로깅 성능을 최적화하고 효율적으로 처리할 수 있는 기능을 제공한다.
- log4j는 부모-자식 계층 구조를 가지며, 부모 로거의 설정을 상속하거나 재정의할 수 있다. 이는 로깅 설정을 중앙에서 관리하고, 특정 패키지 또는 클래스 수준에서 로그 레벨을 미세하게 제어할 수 있는 장점을 제공한다.
- log4j는 thread-safe(멀티스레드 환경에서 사용해도 안전하다).
- log4j는 다양한 로그 레벨, 로그 출력 대상(Appenders), 로그 포맷(Layouts), 로거 계층 구조를 지원하여 로깅을 매우 유연하게 구성할 수 있다.
- log4j는 TRACE, DEBUG, INFO, WARN, ERROR, FATAL 등 다양한 로그 레벨을 제공하여 로그 메시지의 중요도를 쉽게 표현할 수 있다.
- log4j는 다양한 로그 출력 대상(Appenders)을 지원한다. 파일, 콘솔, 데이터베이스, 원격 서버, 이메일 등으로 로그를 출력할 수 있어 모니터링 및 로그 분석을 용이하다.
- log4j는 국제화를 지원.

### 4. Log4j 구조


| 요소 | 설명 |
| --- | --- |
| Logger | 로깅 메세지를 Appender에 전달 ,log4J의 심장부에 위치, 개발자가 직접 로그출력 여부를 런타임에 조정,logger는 로그레벨을 가지고 있으며, 로그의 출력 여부는 로그문의 레벨과 로거의 레벨을 가지고 결정 |
| Appender | 로그의 출력위치를 결정(파일, 콘솔, DB 등), log4J API문서의 XXXAppender로 끝나는 클래스들의 이름을 보면, 출력위치를 어느정도 짐작가능 |
| Layout | Appender가 어디에 출력할 것인지 결정했다면 어떤 형식으로 출력할 것이지 출력 layout을 결정 |

Logger는 실제 로그 기능을 수행하는 객체로 Name을 가지고 사용된다. 

로그 레벨을 설정할 수 있고, 0개 이상의 Appender를 지정할 수 있다. 입력받은 로깅 메시지는 로그 레벨에 따라 Appender로 전달됩니다.

기본적으로 최상위 logger인 Root logger을 설정해 주어야 한다.

| 요소 | 설명 |
| --- | --- |
| jdbc.sqlonly | SQL문만을 로그로 남기며, PreparedStatement일 경우 관련된 argument 값으로 대체된 SQL문이 보여짐. |
| jdbc.sqltiming | SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 포함. |
| jdbc.audit | ResultSet을 제외한 모든 JDBC 호출 정보를 로그로 남긴다. 많은 양의 로그가 생성되므로 특별히 JDBC 문제를 추적해야 할 필요가 있는 경우를 제외하고는 사용을 권장하지 않음. |
| jdbc.resultset | ResultSet을 포함한 모든 JDBC 호출 정보를 로그로 남기므로 매우 방대한 양의 로그가 생성. |

*Appender*는 로그를 출력할 위치, 출력 형식 등을 지정할 수 있다. 

아래 표의 4개 말고도 SMTPAppender, DBAppender 등이 있는데, 이걸 이용하면 로그를 원격 위치에 기록할 수도 있습니다. 그리고 주의해야 할 것이 있는데,  Appender태그는 Logger태그들보다 위에 있어야 합니다.

| 요소 | 설명 |
| --- | --- |
| ConsoleAppender | org.apache.log4j.ConsoleAppender콘솔에 로그 메시지를 출력. |
| FilerAppender | org.apache.log4j.FilerAppender로그 메시지를 지정된 파일에 기록. |
| RollingFileAppender | org.apache.log4j.RollingFileAppender파일 크기가 일정 수준 이상이 되면 기존 파일을 백업파일로 두고 처음부터 다시 기록. |
| DailyRollingFilerAppender | org.apache.log4j.Daily.Rolling.File.Appender일정 기간 단위로 로그 파일을 생성하고 기록. |

*Layout*은 로그를 출력하는 형태를 만들 수 있다.

PatternLayout을 이용하는 게 가장 적합한데, 로그의 출력 형태, Layout을 자신이 원하는 형식으로 바꿀 수 있다.

| 패턴 | 설명 |
| --- | --- |
| %m | 로그 내용 출력 |
| %p | debug, info, warn, error 등의 priority 출력 |
| %r | 어플리케이션 시작 후 이벤트가 발생하는 시점까지의 경과시간. (밀리세컨드로 출력) |
| %c | package 출력 |
| %C | 클래스명 출력 → 성능에 영향을 줌 |
| %d | 이벤트 발생 날짜 출력 → 성능에 영향을 줌 |
| %n | 개행문자(\n) 출력 |
| %M | 로깅이 발생한 method 이름 출력 → 성능에 영향을 줌 |
| %F | 로깅이 발생한 프로그램 파일명 출력 → 성능에 영향을 줌 |
| %l | 로깅이 발생한 caller 정보 출력 → 성능에 영향을 줌 |
| %L | 로깅이 발생한 caller 라인수 출력 → 성능에 영향을 줌 |
| %x | 로깅이 발생한 thread와 관련된 NDC 출력 |
| %X | 로깅이 발생한 thread와 관련된 MDC 출력 |
| % | % 출력 표시 |
| %t | 쓰레드 이름 출력 |

### 5. 결론

- log4j는 자바 애플리케이션에서 로깅을 간편하게 구현하고 관리할 수 있는 강력한 도구로 널리 사용된다
- 다른 로깅 라이브러리와 비교하여 성능 및 확장성 면에서도 우수한 성능을 보여 자주 사용된다.
- 로깅에 대해 자세히 알려보면 아래 링크를 이용해보자
    - https://github.com/Jammini/TIL/blob/master/java/logging.md

### 참고

- https://hajoung56.tistory.com/75
- https://byul91oh.tistory.com/171
