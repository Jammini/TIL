# 자바의 신 VOL.2

**해당 내용은 자바의 신 VOL.2을 보고 내용 정리 하였습니다.**

## 20장 가장 많이 쓰는 패키지는 자바랭

### java.lang 패키지는 특별하다.

#### [자바8 document](https://docs.oracle.com/javase/8/docs/api/java/lang/package-summary.html)

- 자바의 패키지 중에서 유일하게 import를 안해도 사용 할 수 있기 때문이다
- 그만큼 자바에서 꼭 필요한 여러 기능들을 제공함.

자세한 내용은 위에 [document](#자바8-document)를 통해 알아보고 주로 쓰이는 간단한 것만 살펴보자.

### java.lang 패키지에 정의되어 있는 주요 에러를 보자

<details>
<summary>OutOfMemoryError(OOME)</summary>

    메모리가 부족하여 발생하는 에러.
    자바는  가상 머신에서 메모리를 관리하지만, 프로그램을 잘못 작성하거나 설정이 제대로 되어 있지 않을 경우 발생.
</details>    
    
<details>
<summary>StackOverflowError</summary>

    호출된 메소드의 깊이가 너무 깊을때 발생.
    스택이라는 영역에 어떤 메소드가 어떤 메소드를 호출했는지에 대한 정보를 관리.
    특히 재귀 메소드를 잘못 작성하였을 경우 자주 발생하는 에러.
</details>   
    
### java.lang 패키지에서는 기본 어노테이션이 선언되어 있다.

<details>
<summary>Deprecated</summary>

    미리 만들어져 있는 클래스나 메소드가 더 이상 사용되지 않는 경우.
    컴파일러에게 “사용하지 않을 거니까 누가쓰면 경고를 줘” 라고 생각 하면 됨.
</details>  


<details>
<summary>Override</summary>

    해당 메소드가 부모 클래스에 있는 메소드를 Override 했다는 것을 명시적으로 선언.
    여러분들이 Override를 할 때에는 부모 클래스에 있는 메소드의 이름과 매개 변수들을 동일하게 가져감.
    즉, “이 메소드는 Override 된거니까 만약 내가 잘못 코딩했으면 컴파일러 니가 알려줘” 라고 지정 해주는 것.
</details>  

<details>
<summary>Suppress Warnings</summary>

    컴파일러에서 경고(warning)를 알리는 경우가 있다. 프로그램은 문제가 없으니 경고창을 없애고 싶은 경우.
    “얘는 내가 일부러 이렇게 코딩한 거니까 니가 경고를 해 줄 필요가 없어” 라고 하는 것.
</details>  
    
### 숫자를 처리하는 클래스들

<details>
<summary>기본자료형(Primitive Type)</summary>

    자바에서 간단한 계산을 할때는 대부분 기본자료형(Primitive Type)을 사용한다.
    기본자료형은 힙 영역에 저장 되지 않고 스택 영역에 저장되어 관리됨.
</details>  

<details>
<summary>래퍼(Wrapper)클래스</summary>

    기본자료형의 숫자를 객체로 처리해야 할 필요가 있을 수 있다. 그것을 래퍼(Wrapper)클래스 라고 불린다.
    래퍼 클래스는 모두 Number라는 abstract 클래스를 확장(extends).
    자바 컴파일러에서 자동으로 형 변환(오토박싱, 오토언박싱) 해주기 때문에 참조자료형이지만 기본자료형처럼 사용 가능
</details>  
    
<details>
<summary>pase타입이름() 메소드  vs valueOf() 메소드</summary>

    parse타입이름() 메소드는 기본자료형을 리턴하고, valueOf() 메소드는 참조 자료형 리턴
</details> 

### 현재 시간 조회

<details>
<summary>currentTimeMillis()</summary>
 
    - 메소드는 현재 시간을 나타낼 때 매우 유용한 메소드
    - UTC라는 Universal time 기준으로 1970년 1월 1일 00:00부터 지금까지의 밀리초 단위의 차이를 출력
    - 밀리초는 1/1,000 초다. 즉 1,000 밀리초는 1,000ms 로 표시 되며 1초와 동일
</details>

<details>
<summary>nanoTime()</summary>
 
    - 메소드는 현재 시간을 알아내기 위해 사용하는 메소드는 절대 아니고 시간 차이를 측정하기 위한 용도의 메소드.
    - 시간측정할 필요가 있을 때 이용하는 것을 권장.
</details>


### System.out을 살펴보자

```java
public void printNullToString() {
	Object obj = null;
	System.out.println(obj); // null
	System.out.println(obj + " is object's value"); // null is object's value 
									// new StringBuilder().append(obj).append(" is object's value")와 동일하므로 null출력 
	System.out.println(obj.toString()); // NullPointerException
}
```

이와 같이 객체를 출력할 때에는 toString() 사용하는 것보다 valueOf() 메소드 사용이 훨씬 안전함!

---

## 21장 실수를 방지하기 위한 제네릭이라는 것도 있어요

### 제네릭이 뭐지?

- 형 변환에서 발생할 수 있는 문제점을 “사전”에 없애기 위해서 만들어짐.
- 실행시에 예외가 발생하는 것을 처리하는게 아니 컴파일 단계에서 점검할 수 있도록 한 것

### 제네릭 타입의 이름 정하기

- 자바에서 저의한 기본 규칙
    - E : 요소(Element, 자바 컬렉션에서 주로 사용됨)
    - K: 키
    - N: 숫자
    - T: 타입
    - V: 값
    - S,U,V : 두 번째, 세 번째, 네 번째에 선언된 타입

### 제네릭에 ?가 있는것은 뭐야?

- ?를 적어주면 어떤 타입이 제네릭 타입이 되더라도 상관 없다.
- ?로 명시한 타입을 영어로는 wildcard 타입이라고 한다.
