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

---

## 22장 자바랭 다음으로 많이 쓰는 애들은 컬렉션 - Part1(List)

### 자바 컬렉션

자바에서의 데이터를 담는 구조는 크게 다음과 같이 분류할 수 있다.

- 순서가 있는 목록(List) 형 - Collection 인터페이스 구현
- 순서가 중요하지 않은 셋(Set) 형 - Collection 인터페이스 구현
- 먼저 들어온 것이 먼저 나가는 큐(Queue) 형 - Collection 인터페이스 구현
- 키-값(key-value)으로 저장되는 맵(Map) 형

![자바의 컬렉션](https://user-images.githubusercontent.com/59176149/219383356-cfadd6b7-a36f-4106-99c6-d5bf46b0fc69.png)

자바의 컬렉션

```java
public interface Collection<E> extends Iterable<E>
```

위 와 같이 Collection 인터페이스 선언문에서는 특이한 것은 Iterable<E>이라는 인터페이스를 확장(extends) 했다는 점.

Iterable 인터페이스에 선언된어 있는 메소드는 단지 하나다

| 리턴타입 | 메소드 이름 및 매개변수 |
| --- | --- |
| Iterator<T> | iterator() |

- Iterator라는 인터페이스
    - 추가 데이터가 있는지 확인하는 hasNext() 메소드
    - 현재 위치를 다음 요소로 넘기고 그 값을 리턴해주는 next() 메소드
    - 데이터를 삭제하는 remove() 메소드가 있다.

결론적으로, Collection 인터페이스가 Iterable 인터페이스를 확장했다는 의미는, Iterator 인터페이스를 사용하여 데이터를 순차적으로 가져올 수 있다는 의미.

### List 인터페이스와 그 동생들

- List 인터페스를 구현한 클래스(java.util 패키지에서는 ArrayList, Vector, Stack, LinkedList) 중 ArrayList와 Vectort 는 클래스의 사용법은 동일하고 기능도 거의 비슷하다.

| Vector | ArrayList | Stack |
| --- | --- | --- |
| 확장 가능한 배열 | 확장 가능한 배열 | Vector 클래스 확장하여 만든 클래스 |
| JDK 1.0 부터 존재 | JDK 1.2 부터 추가 | LIFO 를 지원하기 위해 만들어짐. |
| Thread Safe 하다 | Thread Safe 하지 않다 |  |

### ArrayList에 대해서 파헤쳐보자

![Untitled](https://user-images.githubusercontent.com/59176149/219384011-59e6d4d6-6b8e-4a5e-af3c-f344a5f58319.png)

- 상속구조를 보면 Object, AbstractCollection, AbstractList 순으로 확장했다. Object를 제외한 나머지 부모클래스들의 이름 앞에서는 Abstact이 붙어 있다. 즉 이 클래스들은 abstract 클래스다.
- 간단히 말하면 일반 클래스와 비슷하지만, 몇몇 메소드는 자식에서 구현하라고 abstract로 선언한 메소드들이 있는 클래스를 말한다. 따라서, AbstractCollection은 Collection 인터페이스 중 일부 공통적인 메소드를 구현해 놓은 것으로 기억하자.

| 인터페이스 | 용도 |
| --- | --- |
| Serializable | 원격으로 객체를 전송하거나. 파일에 저장할 수 있음을 지정 |
| Cloneable | Object 클래스의 clone() 메소드가 제대로 수행될 수 있음을 지정.
즉, 복제가 가능한 객체임을 의미한다. |
| Iterable<E> | 객체가 “foreach” 문장을 사용할 수 있음을 지정 |
| Collection<E> | 여러 개의 객체를 하나의 객체에 담아 처리할 때의 메소드 지정 |
| List<E> | 목록형 데이터에 보다 빠르게 접근할 수 있도록 임의로 접근하는 알고리즘이 적용된다는 것을 지정 |

### ArrayList의 생성자는 3개다

- ArrayList는 “확장 가능한 배열” 이다. 따라서, 배열처럼 사용하지만 대괄호는 사용하지 않고, 메소드를 통해서 객체를 넣고, 빼고, 조회한다.


| 생성자 | 설명 |
| --- | --- |
| ArrayList() | 객체를 저장할 공간이 10개인 ArrayList를 만든다. |
| ArrayList(Collection<? extends E> c | 매개 변수로 넘어온 컬렉션 객체가 저장되어 있는 ArrayList를 만든다. |
| arrayList(int initialCapacity) | 매개 변수로 넘어온 initalCapacity 개수만큼의 저장 공간을 갖는 ArrayList를 만든다. |


```java
ArrayLisy<String list1 = new ArrayList<String>();
// JDK 7부터 생성자를 호출하는 부분(new 뒤에 있는 부분)에 따로 타입을 적지 않고 <>로만 사용해도 된다.
ArrayLisy<String list1 = new ArrayList<>(); 
```

### ArrayList에 데이터를 담아보자

| 리턴 타입 | 메소드 이름 및 매개 변수 | 설명 |
| --- | --- | --- |
| boolean | add(E e) | 매개 변수로 넘어온 데이터를 가장 끝에 담는다. |
| void | add(int index, E e) | 매개 변수로 넘어온 데이터를 지정된 index 위치에 담는다. |
| boolean | addAll(Collection<? extends E> c) | 매개 변수로 넘어온 컬렉션 데이터를 가장 끝에 담는다. |
| boolean | addAll(int index, Collection<? extends E> c | 매개 변수로 넘어온 컬렉션 데이터를 index에 지정된 위치부터 담는다. |

```java
public void checkArrayList4() {
	ArrayList<String> list = new ArrayList<String>();
	list.add("A");
	
	ArrayList<String> list2 = list; // 값만 사용하겠다는게 아닌 list라는 객체가 생성되서 참조되고 있는 주소까지 사용
	list.add("Ooops");
	for(String tempData:list2) {
		System.out.println("List2 " + tempData);
	}
}
// List2 A
// List2 Ooops
```

- list2 = list1 과 같이 다른 객체에 원본 객체의 주소 값만을 할당을 하는 것을 **Shallow copy** 이다.
- 객체의 모든 값을 복사하여 복제된 객체에 있는 값을 변경해도 원본에 영향이 없도록 할 때는 **Deep copy** 수행
- 예를 들어 배열을 복사할때 System 클래스에 있는 arraycopy()와 같은 메소드를 이용하면 Deep copy를 쉽게 처리 할수 있다.
- 따라서, 하나의 Collection 관련 객체를 복사할 일이 있을 때에는 이와 같이 생성자를 사용하거나 addAll() 메소드를 사용할 것은 권장.

### ArrayList에서 데이터를 꺼내자

| 리턴타입 | 메소드 이름 및 매개변수 | 설명 |
| --- | --- | --- |
| Object[] | toArray() | ArrayList 객체에 있는 값들을 Object[] 타입의 배열로 만든다 |
| <T> T[] | toArray(T[] a) | ArrayList 객체에 있는 값들을 매개 변수로 넘어온 T 타입의 배열로 만든다. |

```java
public void checkArrayList6() {
	ArrayList<String> list = new ArrayList<String>();
	list.add("A");
	String[] strList = list.toArray(new String[0]);
	System.out.println(strList[0]);
}
```

```java
public void checkArrayList7() {
	ArrayList<String> list = new ArrayList<String>();
	list.add("A");
	list.add("B");
	list.add("C");
  // tempArray라는 공간에 5개 있는 String 배열 객체를 생성하여 toArray() 메소드의 매개변수로 넘겼다.
	String[] tempArray = new String[3]; 
	// toArray() 메소드의 리턴값을 할당한 strList라는 String 배열에는 list 객체의 데이터 개수 만큼 데이터가 들어가 있다.
	String[] strList = list.toArray(tempArray); 
	for(String temp:strList) {
		System.out.println(temp);
	}
	// A
	// B
	// C
}
```

### ArrayList에 있는 데이터를 삭제하자

| 리턴타입 | 메소드 이름 및 매개변수 | 설명 |
| --- | --- | --- |
| void | clear() | 모든 데이터를 삭제한다. |
| E | remove(int index) | 매개 변수에서 지정한 위치에 있는 데이터를 삭제하고, 삭제한 데이터를 리턴 |
| boolean | remove(Object o) | 매개 변수에 넘어온 객체와 동일한 첫 번째 데이터를 삭제한다. |
| boolean | removeAll(Collection<?> c | 매개 변수로 넘어온 컬렉션 객체이 있는 데이터와 동일한 모든 데이터를 삭제 |

| 리턴타입 | 메소드 이름 및 매개변수 | 설명 |
| --- | --- | --- |
| E | set(int index, E element) | 지정한 위치에 있는 데이터를 두번째 매개 변수로 넘긴 값으로 변경. 그리고 해당 위치에 있던 데이터 리턴 |


### Stack 클래스는 뭐가 다른데?

- List 인터페이스를 구현한 또 하나의 클래스인 Stack 클래스에 대해서 살펴보자.
- LIFO 기능을 구현하려고 할 때 필요한 클래스.

| 리턴타입 | 메소드 이름 및 매개변수 | 설명 |
| --- | --- | --- |
| boolean | empty() | 객체가 비어있는지를 확인 |
| E | peek() | 객체의 가장 위에 있는 데이터를 리턴 |
| E | pop() | 객체의 가장 위에 있는 데이터를 지우고, 리턴 |
| E | push(E item) | 매개변수로 넘어온 데이터를 가장 위에 저장 |
| int | search(Object o) | 매개변수로 넘어온 데이터의 위치 리턴 |

