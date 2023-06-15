# Mybatis #과 $차이

### 목차

1. [개요](#1-개요)
2. [#{} 실행방식](#2--실행방식)
3. [${} 실행방식](#3--실행방식)
4. [결론](#4-결론)

### 1. 개요

- MyBatis는 데이터베이스 접근 라이브러리이고 데이터베이스와 상호 작용할 때, SQL쿼리를 작성하고 실행하는 방법에 대한 기능을 제공한다.
- 주로 SQL 쿼리 내에서 2가지 기호로 ‘#’, ‘$’을 사용하는데, 각각 다른 목적과 동작 방식을 가지고 있으며 살펴보도록 하자.

### 2. #{} 실행방식

- SQL 쿼리의 매개 변수(parameter)를 나타내는 데 사용된다.
- SQL 쿼리에 대한 자동화된 바인딩을 제공하며, 입력 매개 변수를 자동으로 이스케이프(escape)하여 SQL 쿼리에 삽입한다.
- Prepared Statement를 사용하여 SQL을 실행한다.
- SQL구문을 미리 컴파일 하여 성능을 최적화하는 기능을 제공한다.

### 3. ${} 실행방식

- SQL 쿼리의 리터럴(literal) 값을 나타내는 데 사용된다.
- 매개 변수를 문자열로 치환한 후, SQL 쿼리에 직접 삽입한다. 그래서 보안에 취약할 수 있어 주의가 필요하다.
- Statement를 사용하여 SQL을 실행한다.
- SQL 인젝션(SQL Injection)과 같은 보안 취약점에 노출될 수도 있으므로 주의가 필요하다.

### 4. 결론

- ‘#’과 ‘$’의 선택은 주어진 상황과 사용 목적에 따라 적절히 사용해야 한다.
- 일반적으로 매개 변수나 사용자 입력과 같이 외부로 받은 값은 ‘#’을 사용하여 안전하게 처리하는 것이 좋다.
- 반면, 정적인 값을 직접 SQL에 삽입해야 할 경우 ‘$’을 사용할 수 있으나 SQL인젝션과 같은 보안 취약점에 노출되지 않도록 검증과 필터링을 걸쳐야한다.
- Statement와 PreparedStatement에 대해 자세히 알아보고 싶다면 아래 링크를 통해 살펴보자.
    - https://github.com/Jammini/TIL/blob/master/java/statement_vs_preparedStatement.md

### 참고

- [https://grandma-coding.tistory.com/entry/MyBatis-와-의-차이점](https://grandma-coding.tistory.com/entry/MyBatis-%EC%99%80-%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- https://mine-it-record.tistory.com/300
