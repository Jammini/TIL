# Statement vs PreparedStatement

### 목차

1. [개요](#1-개요)
2. [JDBC API 인터페이스](#2-jdbc-api-인터페이스)

    2-1. [Statement](#1-statement)
    
    2-2. [PreparedStatement](#2-preparedstatement)
    
3. [결론](#3-결론)

## 1. 개요

*Statement*와 *PreparedStatement* 인터페이스는 모두 SQL 쿼리를 실행하는 데 사용된다. 하지만, 두 인터페이스의 차이를 보안과 성능의 관점에서 살펴보자.

## 2. JDBC API 인터페이스

두개의 인터페이스는 어떻게 동작할까?

1. 쿼리 문장 분석
2. 컴파일
3. 실행

### 1. Statement

1. **문자열 기반 SQL 쿼리를 실행**한다.

```java
public void insert(PersonEntity personEntity) {
    String query = "INSERT INTO persons(id, name) VALUES(" + personEntity.getId() + ", '"
      + personEntity.getName() + "')";

    Statement statement = connection.createStatement();
    statement.executeUpdate(query);
}
```

2. **캐시 사용 X** - 성능 측면
- 쿼리 수행시 위의 1 ~ 3 단계를 거치 매번 쿼리마다 실행한다.
3. **SQL 인젝션 취약** - 보안 측면

*SQL 인젝션에 대해서 모른다면 [링크](https://namu.wiki/w/SQL%20injection)를 타고 알아보자

```java
dao.update(new PersonEntity(1, "hacker' --")); // -- 이후 모든 항목이 주석으로 처리되어 업데이트 문의 조건이 무시됨.
dao.insert(new PersonEntity(1, "O'Brien")) // 따옴표가 이스케이프가 되지 않기때문에 삽입 실패
```

4. **DDL 적합**

```java
public void createTables() {
    String query = "create table if not exists PERSONS (ID INT, NAME VARCHAR(45))";
    connection.createStatement().executeUpdate(query);
}
```

### 2. PreparedStatement

1. **매개변수화된 SQL 쿼리를 실행**한다.

```java
public void insert(PersonEntity personEntity) {
    String query = "INSERT INTO persons(id, name) VALUES( ?, ?)";

    PreparedStatement preparedStatement = connection.prepareStatement(query);
    preparedStatement.setInt(1, personEntity.getId());
    preparedStatement.setString(2, personEntity.getName());
    preparedStatement.executeUpdate();
}
```

파일 및 배열을 포함하여 다양한 객체 유형을 바인딩하는 방법이 있다.

2. **캐시 사용 O** - 성능 측면
- 쿼리 수행시 처음 한 번만 1 ~ 3 단계를 거친 후 캐시에 담아 재사용한다.
- DB 쿼리를 받는 즉시 쿼리를 미리 컴파일 하기 전에 캐시를 확인한다. 캐시되지 않은 경우, DB 엔진은 다음 사용을 위해 저장해 둔다.
3. **SQL 인젝션 안전** - 보안 측면

```java
@Test 
void whenInsertAPersonWithQuoteInText_thenItNeverThrowsAnException() {
    assertDoesNotThrow(() -> dao.insert(new PersonEntity(1, "O'Brien")));
}

@Test 
void whenAHackerUpdateAPerson_thenItUpdatesTheTargetedPerson() throws SQLException {

    dao.insert(Arrays.asList(new PersonEntity(1, "john"), new PersonEntity(2, "skeet")));
    dao.update(new PersonEntity(1, "hacker' --"));

    List<PersonEntity> result = dao.getAll();
    assertEquals(Arrays.asList(
      new PersonEntity(1, "hacker' --"), 
      new PersonEntity(2, "skeet")), result);
}
```

- 제공되는 모든 매개변수 값에 대한 텍스트를 이스케이프 처리하여 SQL 인젝션으로 부터 안전하다.

## 3. 결론

- *Statement*와 *PreparedStatement*의 주요 차이점에 대해 알아보았다.
- 살펴봤듯이 성능과 보안관점에서 차이가 있으며, DDL쿼리에는 *Statement*를 사용하고 DML 쿼리에는 *PreparedStatement*를 사용하는 것이 적합하다.

### 참고

- [https://www.baeldung.com/java-statement-preparedstatement](https://www.baeldung.com/java-statement-preparedstatement)
- [https://devbox.tistory.com/entry/Comporison](https://devbox.tistory.com/entry/Comporison)
- [https://webstone.tistory.com/56](https://webstone.tistory.com/56)
- [https://namu.wiki/w/SQL injection](https://namu.wiki/w/SQL%20injection)
