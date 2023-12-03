# 상속 vs 컴포지션 - 객체 간의 관계를 나타내는 두 가지 방법

### 목차

1. [상속](#1-상속)
2. [컴포지션](#2-컴포지션)
3. [차이점 (상속 vs 컴포지션)](#3-차이점-상속-vs-컴포지션)
4. [결론](#4-결론)

상속과 컴포지션은 객체지향 프로그래밍에서 객체 간의 관계를 나타내는 두 가지 다른 방법으로, ‘적절한 상황’에서 ‘적절한 방법’을 사용해야 한다.

### 1. 상속

상속은 기존 클래스의 기능을 재사용하여 새로운 클래스를 만드는 방법이다. 

새로운 클래스는 기존 클래스의 필드와 메소드를 상속받아 사용할 수 있으며, 이를 확장하여 새로운 기능을 추가할 수 있다. 

상속은 `is-a` 관계를 나타내며, 상위 클래스와 하위 클래스의 관계를 나타낸다.

- 장점
    1. 코드의 재사용을 통해 중복을 줄인다.
    2. 확장성이 증가한다.
    3. 클래스 간의 계층적 관계를 구성함으로써 다형성을 구현할 수 있다.
- 문제점
    <details>
    <summary>1. 캡슐화를 위반할 수 있다.</summary>
    <pre>
    부모 클래스의 구현 세부사항을 자식 클래스가 직접 접근할 수 있기 때문에 캡슐화 원칙을 위반할 수 있다. 이러한 문제를 해결하기 위해 접근 제어자를 사용하거나 인터페이스를 활용하여 캡슐화를 유지할 수 있다.
    </pre>
    </details>
    
    <details>
    <summary>2. 상속의 계층 구조가 복잡해질 수 있다.</summary>
    <pre>
    상속을 남발하면 클래스의 계층 구조가 복잡해지고 유지보수가 어려워질 수 있다. 또한 상속의 깊이가 깊어지면 컴파일 시간과 실행 시간이 느려질 수 있다.
    </pre>
    </details>

    <details>
    <summary>3. 부적절한 메서드 오버라이딩이 발생 할 수 있다.</summary>
    <pre>
    자식 클래스가 부모 클래스의 메서드를 오버라이딩할 때, 오버라이딩한 메서드가 원래 메서드와 동일한 이름을 가지므로 잘못된 오버라이딩으로 인해 의도하지 않은 동작이 발생할 수 있다.
    </pre>
    </details>

    <details>
    <summary>4. 다중 상속이 지원되지 않는다.</summary>
    <pre>
    자바는 다중 상속을 지원하지 않기 때문에 여러 클래스를 동시에 상속할 수 없다. 이를 해결하기 위해  인터페이스를 사용할 수 있다.
    </pre>
    </details>

    <details>
    <summary>5. 상속 관계가 복잡해질수록 유지보수가 어려워질 수 있다.</summary>
    <pre>
    상속관계가 복잡해질수록 클래스 간의 의존성이 높아지며, 이로 인해 유지보수가 어려워질 수 있다. 따라서 상속을 사용할 때는 상속의 사용범위와 목적을 명확히 고려해야 한다.
    </pre>
    </details>
        
- 예시코드

```java
public class MyHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }
}
```

```java
MyHashSet<String> myHashSet = new MyHashSet<>();
myHashSet.addAll(Arrays.asList("A", "B", "C"));

System.out.println(myHashSet.getAddCount()); // 6 출력
```

- ‘A, B, C’ 3개의 리스트를 addAll 했을 경우, addCount가 3이 찍힐 것으로 예상했으나 6이 출력되는 것을 확인 할 수 있다.
- 왜냐하면, 아래 addAll의 부모클래스를 보면 알 수 있다.

    <img width="516" alt="image" src="https://user-images.githubusercontent.com/59176149/224008314-cf5f1d9e-f250-4bce-b422-792368af76d7.png">


- 파라미터로 받은 컬렉션들의 리스트의 개수 만큼 add 메소드를 사용하기 때문이다.

### 2. 컴포지션

컴포지션은 객체가 다른 객체를 포함하고 있는 관계를 나타내는 방법 객체 안에 다른 객체를 멤버 변수로 선언하고, 해당 객체의 메서드를 호출하여 사용한다.

컴포지션은 객체 안에 다른 객체를 포함하고 있는 일종의 `has-a` 관계를 표현한다.

- 장점
    1. 코드 재사용성
    2. 유연성
    3. 객체 간의 결합도 감소
    4. 코드의 가독성과 유지보수성
- 문제점
    <details>
    <summary>1. 상속보다 비직관적</summary>
    <pre>
    컴포지션은 상속보다 비직관적인 경우가 있다. 특히, 컴포지션을 사용하는 경우 객체 간의 관계를 런타임 시에 결정하기 때문에 코드를 이해하기 어렵게 만들 수 있다.
    </pre>
    </details>
    <details>
    <summary>2. 복잡성</summary>
    <pre>
    컴포지션을 사용하면 객체 간의 관계가 복잡해질 수 있다. 이는 객체 간의 의존성을 매우 세부적으로 정의할 수 있게 하지만, 동시에 객체 간의 관계를 이해하기 어렵게 만들 수도 있다.
    </pre>
    </details>
    <details>
    <summary>3. 메모리관리</summary>
    <pre>
    컴포지션을 사용하면 객체가 다른 객체를 참조하므로, 객체가 소멸될 때 다른 객체도 함께 소멸되어야 한다. 이를 관리하기 위해서는 적절한 메모리 관리가 필요하다.
    </pre>
    </details>
        
- 예시코드

```java
public class Customer {
    private String name;
    private String address;
    private String phone;

    public Customer(String name, String address, String phone) {
        this.name = name;
        this.address = address;
        this.phone = phone;
    }

    public String getName() {
        return name;
    }
}

public class Order {
    private Customer customer;
    private int orderNumber;
    private double orderTotal;

    public Order(Customer customer, int orderNumber, double orderTotal) {
        this.customer = customer;
        this.orderNumber = orderNumber;
        this.orderTotal = orderTotal;
    }

    public void printInvoice() {
        // Invoice 출력 코드
        System.out.println("Customer Name: " + customer.getName());
        System.out.println("Order Number: " + orderNumber);
        System.out.println("Order Total: " + orderTotal);
    }
}

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("John Doe", "123 Main St", "555-555-1212");
        Order order = new Order(customer, 1, 100.0);
        order.printInvoice();
    }
}
```

- 위 코드는 Order 클래스와 Customer 클래스가 서로 강하게 결합되어 있다.
- 만약 Customer 클래스의 getName() 메소드가 변경된다면, Order 클래스의 printInvoice() 메소드도 수정해주어야 한다.
- 이는 유지보수성이 떨어지는 코드를 만들어내게 된다.

### 3. 차이점 (상속 vs 컴포지션)

1. 관계의 형태
    - 상속은 부모 클래스와 자식 클래스 사이에 계층 구조를 만들어내는 일종의 `is-a` 관계를 표현한다.
    - 컴포지션은 객체 안에 다른 객체를 포함하고 있는 일종의 `has-a` 관계를 표현한다.
2. 객체 간의 결합도
    - 상속은 부모 클래스의 수정이 자식 클래스에도 영향을 미칠 수 있기 때문에 상속 구조가 복잡해질수록 유지보수가 어려워질 수 있다.
    - 컴포지션은 (상속에 비해) 객체간의 결합도가 낮기 때문에 수정이 쉽고 유연합니다.

### 4. 결론

- 상속은 명확한 `is - a` 관계에 있는 경우 사용하자.
- 상속은 상위 클래스가 확장할 목적으로 설계되었고 문서화가 잘되어 있다면 사용하자.
- **따라서, 이펙티브 자바의 저자 조슈아 블로치의 말 처럼 위의 두 가지가 명확하지 않다면 `상속보다는 조합(컴포지션)을 사용하라.`**

### 참고

- Effective Java 3rd Edition - Item 18.
- [https://www.youtube.com/watch?v=YJ4JJsGy8rY](https://www.youtube.com/watch?v=YJ4JJsGy8rY)

