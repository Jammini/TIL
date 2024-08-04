 # JPA를 사용하는 이유가 무엇일까?

### 목차

1. [개요](#1-개요)
2. [순수 JDBC](#2-순수-jdbc)
3. [SQL Mapper(JdbcTemplate, Mybatis)](#3-sql-mapperjdbctemplate-mybatis)
4. [JPA(Java Persitence Api)](#4-jpajava-persitence-api)
5. [Jdbc vs SQL Mapper vs JPA](#5-jdbc-vs-sql-mapper-vs-jpa)
6. [JPA가 무조건 좋을까?](#6-jpa가-무조건-좋을까)
7. [결론](#7-결론)

### 1. 개요

현재 많은 개발자들이 JPA를 사용하지만 JPA를 사용하는 이유와 장단점에 대해 정리해보려고 한다.

먼저 코드를 통해서 눈에 띄는 차이를 살펴보자.

### 2. 순수 JDBC

<img width="773" alt="image" src="https://github.com/user-attachments/assets/76b1dafc-b7e5-49bc-bec0-2d7752a1a426">

과거에는 객체를 데이터베이스 저장 하려면 위와 같이 복잡한 JDBC API와 SQL을 한땀 한땀 작성해야 했다.

### 3. SQL Mapper(JdbcTemplate, Mybatis)

<img width="776" alt="image" src="https://github.com/user-attachments/assets/18a0528d-8a14-46ac-90ef-d588a7f6e77f">

JdbcTemplate이나 Mybatis 같은 SQL Mapper가 등장하면서 위와 같이 개발 코드가 줄었지만 개발자가 SQL을 작성해야 하는 것은 변하지 않았다.

### 4. JPA(Java Persitence Api)

<img width="571" alt="image" src="https://github.com/user-attachments/assets/d46a2403-2773-474c-8a4a-3c1c327c195f">

이처럼 Java 컬렉션에 객체를 저장하고 조회하는 것처럼 단순하게 JPA를 이용하면 이렇게 간단해진다.

JPA가 개발자 대신에 적절한 SQL을 생성하고 데이터베이스에 실행해서 객체를 저장하거나 불러오게 된다.

### 5. Jdbc vs SQL Mapper vs JPA

<img width="774" alt="image" src="https://github.com/user-attachments/assets/1731e605-e2c9-4a73-8d77-96cc936c2ca1">

SQL을 한땀 한땀 작성하는 것은 개발 생산성측면에서 뒤떨어질 수 밖에 없다.

호미랑 삽을 이용해 농사를 하거나, 소를 데리고 농사를 해도 트랙터로 농사를 하는 것이 편리함을 많이 느낄 것이다.

### 6. JPA가 무조건 좋을까?

JPA의 이점도 많지만 장단점을 살펴보고 그에 맞게 적용하자.

**장점**

1. 쿼리를 하나하나 작성할 필요가 없어 코드량이 줄어든다.
2. 객체 지향적인 접근 지원
3. DBMS에 독립적 → DB가 변경되어도 SQL문을 다시 작성X

**단점**

1. 높은 학습곡선
2. 복잡한 SQL 생성의 어려움
3. 성능에 대한 고려 필요

### 7. 결론

이렇게 JPA를 사용하는 이유와 장단점에 대해서 알아봤다.

위와 같이 장단점을 고려하여 실제 프로젝트에서는 특성, 개발자의 선호도, 팀의 스킬셋 등을 고려하여 이루어지면 될 것이다.

### 참고

- https://www.inflearn.com/course/ORM-JPA-Basic/dashboard
- https://www.elancer.co.kr/blog/view?seq=231