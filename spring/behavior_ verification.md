# 상태검증 vs 행위 검증

### 목차

1. 상태검증이란?
2. 행위검증이란?

### 1. 상태검증이란?

- 메서드가 수행될 때 **연관되어있는 협력 객체의 '상태'를 검증**함으로써 제대로 기능이 동작하고 있는지를 검증하는 것이다.

```java
val abc = Abc()
abc.increase()
assertThat(abc.value).isEqualTo(10)
```

abc 인스턴스의 value가 10과 같은지 상태를 검증한다.

### 2. 행위 검증이란?

- 테스트하고자 하는 메소드가 **참조하고 있는 협력 객체의 메소드를 제대로 호출 하는지에 대한 '행위'를 검증**하는 것이다.

```java
val abc = Abc()
abc.increase()
verify(abc, atLeastOnce()).increase()
```

abc 인스턴스의 increase() 메서드가 최소 한 번은 수행되었는지 행위를 검증한다.
