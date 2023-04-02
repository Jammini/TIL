# 자바의 리스트들은 어떻게 동작할까?(ArrayList, LinkedList)

### 목차

1. [ArrayList란?](#1-arraylist란)
2. [ArrayList는 어떻게 동작하는가?](#2-arraylist는-어떻게-동작하는가)
    
    [2-1. 생성](#1-생성)
    
    [2-2. 추가](#2-추가)
    
    [2-3. 삭제](#3-삭제)
    
    [2-4. 조회](#4-조회)
    
3. [LinkedList란?](#3-linkedlist란)
4. [LinkedList는 어떻게 동작하는가?](#4-linkedlist는-어떻게-동작하는가)
    
    [4-1. 추가](#1-추가)
    
    [4-2. 삭제](#2-삭제)
    
    [4-3. 조회](#3-조회) 
    
5. [결론](#5-결론)

## 1. ArrayList란?

- List 인터페이스의 구현체 배열이다.
- 중복을 허용하고 순서를 유지하며 인덱스로 원소를 관리한다.
- 배열기반의 리스트이며 일반 배열과 달리 동적이라 크기를 할당하지 않아도 데이터를 추가하거나 삭제할 수 있다.

## 2. ArrayList는 어떻게 동작하는가?

ArrayList의 주요 메서드인 생성, 추가, 삭제, 조회가 어떻게 동작하는지 알아보자.

### 1. 생성

<img width="271" alt="image" src="https://user-images.githubusercontent.com/59176149/226629284-519d39e5-0032-4bbe-b0ad-f60aad89d96a.png">

- 생성자는 세개가 존재하지만 기본 생성자에 대해서만 간단하게 다루어 보겠다.

```java
/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

- `List<Integer> list = new ArrayList<>()`선언시 초기 capacity가 10인 빈 리스트를 생성하다고 주석처리 되어있다.
- 주의할점은 리스트의 size는 리스트의 사이즈가 아니라 리스트에 포함하고 있는 원소의 개수인 점이다.
- 즉, ArrayList 선언시 배열의 크기가 아래와 같이 10으로 만들어 진다는 것이다.

<img width="455" alt="image" src="https://user-images.githubusercontent.com/59176149/226629457-a460e2c8-8773-4ad5-bf5b-13caab077510.png">

### 2. 추가

- 리스트 끝에 데이터 추가

```java
/**
 * Appends the specified element to the end of this list.
 *
 * @param e element to be appended to this list
 * @return <tt>true</tt> (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```

```java
/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

- 기본 list가 10개 가득 차있고 10을 추가한다고 가정해보자.
- newCapacity가 기존에 있는 oldCapacity에 오른쪽 비트 연산을 이용해 1.5배 만큼 설정을 해준다.
- 기본적으로 배열의 크기가 정해져 있어 데이터를 추가할 경우 O(1)이 걸리겠지만 임계치를 넘게 되면 아래와 같이 다른 배열로 값이 복사 된 후 데이터가 추가가 된다. 이때 시간복잡도는 O(N)이 되는 것이다.

<img width="714" alt="image" src="https://user-images.githubusercontent.com/59176149/226629562-ea7b7a6b-cfb2-4d97-a4ed-961cb3eee4fa.png">

- 10을 중간 인덱스에 추가한다고 가정하면, 아래와 같이 인덱스를 다 밀어서 넣어주어야 하므로 중간에 데이터를 넣는 경우는 무조건 O(N)이 된다.

<img width="509" alt="image" src="https://user-images.githubusercontent.com/59176149/226629643-cc8bafa7-9a9e-4150-8a78-557038841c87.png">

### 3. 삭제

- 매개변수의 index에 원소를 삭제

```java
/**
 * Removes the element at the specified position in this list.
 * Shifts any subsequent elements to the left (subtracts one from their
 * indices).
 *
 * @param index the index of the element to be removed
 * @return the element that was removed from the list
 * @throws IndexOutOfBoundsException {@inheritDoc}
 */
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}
```

- 먼저 인덱스를 체크해주고 삭제할 원소 이후의 인텍스를 이동시켜주고 마지막 원소는 null 처리를 해준다.
- 삭제한 원소로부터 앞으로 한 칸씩 이동시키므로 시간복잡도는 O(N)이 된다.

<img width="330" alt="image" src="https://user-images.githubusercontent.com/59176149/226630072-93a33c3e-6033-434b-b835-3c23a6fc4de1.png">

### 4. 조회

- get 메소드는 인덱스의 지정된 위치에 요소를 가져온다.
    - 시간복잡도는 O(1)을 가진다.
- Contains 메소드는 매개변수가 존재하면 true를 그렇지 않으면 false를 반환한다.

```java
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}

public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}
```

- Contains 메소드의 같은 경우는 값이 들어 있는지 봐야하기 때문에 전부다 조회를 해야한다.
    - 시간복잡도는 O(N)을 가진다.

## 3. LinkedList란?

- 연결된 노드들의 집합
- 순서를 가르키는 포인터와 데이터로 존재한다.

## 4. LinkedList는 어떻게 동작하는가?

LinkedList는 ArrayList와 달리 빈 리스트를 생성하며 마찬 가지로 주요메서드인 추가, 삭제, 조회가 어떻게 동작하는지 알아보자.

### 1. 추가

- 리스트 끝에 데이터 추가

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
/**
 * Links e as last element.
 */
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

- 만약 4를 추가 한다고 가정하면, 끝에 추가 할 경우 노드를 연결만 해주면 되기 때문에 시간복잡도는 O(1)이 걸린다.

<img width="477" alt="image" src="https://user-images.githubusercontent.com/59176149/226630294-74b7df4c-dbe1-4829-ba2a-edf8a324b0c1.png">

- 데이터를 중간에 추가

```java
public void add(int index, E element) {
    checkPositionIndex(index);

    if (index == size)
        linkLast(element);
    else
        linkBefore(element, node(index));
}
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    final Node<E> pred = succ.prev;
    final Node<E> newNode = new Node<>(pred, e, succ);
    succ.prev = newNode;
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode;
    size++;
    modCount++;
}
```

- 그러나, 데이터 4를 리스트 2와 3사이 중간에 추가하고 싶다면, 0부터 2까지 순회를 하고 그 다음 노드를 추가하고 포인터를 변경해줘야 한다. 그러므로 이런식으로 추가하면 시간복잡도는 O(N)이 된다.

<img width="457" alt="image" src="https://user-images.githubusercontent.com/59176149/226630390-40c8314f-e2c4-43af-b62d-31dd449e51c2.png">

### 2. 삭제

- 값을 가지고 있는 데이터를 찾아 삭제한다.
- 단순히 LinkedList를 보면 노드를 삭제하고 포인터를 연결만 하면 되기 때문에자료구조의 의미로는 시간복잡도가 O(1)이지만, 프로그래밍상에서는 O(N)이 된다.

```java
public boolean remove(Object o) {
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}
```

- 결국에 삭제가 이루어지려면 그 데이터를 가지고 있는 노드를 찾아야하기에 조회를 한 후 삭제가 이루어 진다.
- 2라는 데이터를 삭제한다고 가정할때, 2라는 데이터를 찾아내서 삭제가 이루어지는 것이다.

<img width="392" alt="image" src="https://user-images.githubusercontent.com/59176149/226630696-316d249b-b127-4c0e-9fea-07201dd5acce.png">

### 3. 조회

- 찾으려는 노드가 있다면 노드를 뒤져가며 순차적으로 찾아야 하기때문에 O(N)이 소요된다.

## 5. 결론

- 전반적으로 봤을때 LinkedList보다 ArrayList가 조금 더 효율적이다.
- 인덱스 기반의 꺼내는 연산이 많이 이루어질 경우, ArrayList가 유리하다.
- ArrayList와 LinkedList가 제공하는 메소드를 더 자세하게 알고 싶다면 아래 오라클에서 제공하는 사이트나 IDE내에서 확인해보자.

### 참고

- [https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html)
- [https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html)
