# Junit5은 무엇일까?

### 목차

1. [JUnit은 무엇인가?](#1-junit은-무엇인가)
2. [JUnit5은 무엇인가?](#2-junit5은-무엇인가)
3. [JUnit5의 주요 어노테이션](#3-junit5의-주요-어노테이션)
4. [JUnit5의 Assertions](#4-junit5의-assertions)
5. [결론](#5-결론)

### 1. JUnit은 무엇인가?

JUnit은 자바 프로그래밍 언어용 유닛 테스트 프레임워크다.

Jetbrain에서 발표한 통계에 따르면 자바 개발자의 86%가 JUnit을 사용하고 있다고 한다.

![image](https://github.com/Jammini/TIL/assets/59176149/9ba9d9e7-5ef8-4246-b279-c2eb1fca7b6c)

### 2. JUnit5은 무엇인가?

JUnit5에서 5는 버전을 뜻하고 2017년 2월에 출시하여 많은 자바 개발자들이 사용하고 있는 테스트 기반 프레임워크이다.

JUnit5은 자바8 이상부터 사용가능하며 JUnit Platform과 JUnit Jupiter, Junit Vintage 결합한 형태라고 보면 된다.

![image](https://github.com/Jammini/TIL/assets/59176149/cd7edfbe-a4ed-4c6d-be97-6b0d105abba7)

JUnit Platform: 테스트를 실행해주는 런처와 TestEngine API를 제공함.

Jupiter: TestEngine API 구현체로 JUnit5에서 제공함.

Vintage: TestEngine API 구현체로 JUnit3, 4에서 제공함.

### 3. JUnit5의 주요 어노테이션

| JUnit5 어노테이션 | 내용 | JUnit4 어노테이션 |
| --- | --- | --- |
| @Test | 테스트 Method임을 선언함. | @Test |
| @ParameterizedTest | 매개변수를 받는 테스트를 작성할 수 있음. |  |
| @RepeatedTest | 반복되는 테스트를 작성할 수 있음. |  |
| @TestFactory | @Test로 선언된 정적 테스트가 아닌 동적으로 테스트를 사용함. |  |
| @TestInstance | 테스트 클래스의 생명주기를 설정함. |  |
| @TestTemplate | 공급자에 의해 여러 번 호출될 수 있도록 설계된 테스트 케이스 템플릿임을 나타냄. |  |
| @TestMethodOrder | 테스트 메소드 실행 순서를 구성하는데 사용함. |  |
| @DisplayName | 테스트 클래스 또는 메소드의 사용자 정의 이름을 선언할 때 사용함. |  |
| @DisplayNameGeneration | 이름 생성기를 선언함. 예를 들어 '_'를 공백 문자로 치환해주는 생성기가 있음. ex ) new_test -> new test |  |
| @BeforeEach | 모든 테스트 실행 전에 실행할 테스트에 사용함. | @Before |
| @AfterEach | 모든 테스트 실행 후에 실행한 테스트에 사용함. | @After |
| @BeforeAll | 현재 클래스를 실행하기 전 제일 먼저 실행할 테스트 작성하는데,  static로 선언함. | @BeforeClass |
| @AfterAll | 현재 클래스 종료 후 해당 테스트를 실행하는데,  static으로 선언함. | @AfterClass |
| @Nested | 클래스를 정적이 아닌 중첩 테스트 클래스임을 나타냄. |  |
| @Tag | 클래스 또는 메소드 레벨에서 태그를 선언할 때 사용함.  이를 메이븐을 사용할 경우 설정에서 테스트를 태그를 인식해 포함하거나 제외시킬 수 있음. |  |
| @Disabled | 이 클래스나 테스트를 사용하지 않음을 표시함. | @Ignore |
| @Timeout | 테스트 실행 시간을 선언 후 초과되면 실패하도록 설정함. |  |
| @ExtendWith | 확장을 선언적으로 등록할 때 사용함. |  |
| @RegisterExtension | 필드를 통해 프로그래밍 방식으로 확장을 등록할 때 사용함. |  |
| @TempDir | 필드 주입 또는 매개변수 주입을 통해 임시 디렉토리를 제공하는데 사용함. |  |

```java
public class SampleSuccessTests {

    @BeforeAll
    static void setup() {
        System.out.println("@BeforeAll - executes once before all test methods in this class");
    }

    @BeforeEach
    void init() {
        System.out.println("@BeforeEach - executes before each test method in this class");
    }

    @AfterEach
    void tearDown() {
        System.out.println("@AfterEach - executed after each test method.");
    }

    @AfterAll
    static void tearDownAll() {
        System.out.println("@AfterAll - executed after all test methods.");
    }

    @Test
    void successTest1() {
        System.out.println("executes successTest1");
        assumeTrue("abc".contains("a"));
    }

    @Test
    void successTest2() {
        System.out.println("executes successTest2");
        assumeTrue("abc".contains("b"));
    }
}
```

위 코드를 실행하면 아래와 같은 결과가 나오게 된다.

![image](https://github.com/Jammini/TIL/assets/59176149/6088ccb4-f55d-4bf5-b7d4-b359327b4fd7)

### 4. JUnit5의 Assertions

JUnit5 Jupiter Assertions 메소드는 모두 static 메소드라는 특징이 있으며, 자세한 모든 메소드는 **[이곳](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html)**에서 확인하실 수 있다.

자주 사용하는 메소드만 살펴보자.

**(1) 기본**

```java
@TestvoidstandardAssertions() {
        assertEquals(2, calculator.add(1, 1));
        assertEquals(4, calculator.multiply(2, 2),
                "The optional failure message is now the last parameter");
        assertTrue('a' < 'b', () -> "Assertion messages can be lazily evaluated -- "
                + "to avoid constructing complex messages unnecessarily.");
    }
```

`assertEquals()`를 사용하여 값이 같은 값인지 비교한다.

`assertTrue()`도 같은 맥락으로, 조건 값이 True인지 확인한다.

**(2) 그룹**

```java
@TestvoidgroupedAssertions() {
// In a grouped assertion all assertions are executed, and all// failures will be reported together.
        assertAll("person",
            () -> assertEquals("Jane", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName())
        );
    }

@TestvoiddependentAssertions() {
// Within a code block, if an assertion fails the// subsequent code in the same block will be skipped.
        assertAll("properties",
            () -> {
                String firstName = person.getFirstName();
                assertNotNull(firstName);

// Executed only if the previous assertion is valid.
                assertAll("first name",
                    () -> assertTrue(firstName.startsWith("J")),
                    () -> assertTrue(firstName.endsWith("e"))
                );
            },
            () -> {
// Grouped assertion, so processed independently// of results of first name assertions.
                String lastName = person.getLastName();
                assertNotNull(lastName);

// Executed only if the previous assertion is valid.
                assertAll("last name",
                    () -> assertTrue(lastName.startsWith("D")),
                    () -> assertTrue(lastName.endsWith("e"))
                );
            }
        );
    }
```

`assertAll()`을 이용해 여러 개의 assertions가 만족할 경우에만 테스트를 통과하였다고 판단하고 싶을 경우 사용한다. 그리고 이 메소드 내에서 인자로 람다식을 사용하며, 여러 개의 람다식이 동시에 실행되는 특징을 가지고 있다.

**(3) 예외 처리**

```java
@TestvoidexceptionTesting() {
        Exception exception = assertThrows(ArithmeticException.class, () ->
            calculator.divide(1, 0));
        assertEquals("/ by zero", exception.getMessage());
    }
```

`assertThrows()`는 특정 예외가 발생하였는지 확인하고 싶을 때 사용한다.

**(4) 시간 초과**

```java
@TestvoidtimeoutNotExceeded() {
// The following assertion succeeds.
        assertTimeout(ofMinutes(2), () -> {
// Perform task that takes less than 2 minutes.
        });
    }
```

```java
@TestvoidtimeoutExceededWithPreemptiveTermination() {
// The following assertion fails with an error message similar to:// execution timed out after 10 ms
        assertTimeoutPreemptively(ofMillis(10), () -> {
// Simulate task that takes more than 10 ms.new CountDownLatch(1).await();
        });
    }
```

`assertTimeout()`, `assertTimeoutPreemptively()`은 assertions가 특정 시간 안에 실행이 끝났는지 확인하고 싶을 때 사용한다. 전자는 단순히 특정 시간 안에 실행이 끝나는지만 확인하지만, 후자는 특정 시간 안에 실행이 끝나지 않으면 바로 종료해 버리는 특징이 있다.

### 5. 결론

JUnit5은 테스트 프레임워크로서 테스트 작성과 관리를 더 효과적으로 수행할 수 있도록 다양한 기능과 확장성을 제공한다. 그로 인해 코드 품질을 높이고 소프트웨어의 신뢰성을 향상시키는데 도움이 된다.

### 참고

- https://www.youtube.com/watch?v=uqZDt-0BDAo
- https://steady-coding.tistory.com/349
- https://wecandev.tistory.com/168#google_vignette
