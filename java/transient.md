# 자바에서 transient 는 어떤 키워드 인가?

### 목차

1. [직렬화와 역질력화](#1-직렬화와-역직렬화)
2. [transient란?](#2-transient란)
3. [transient는 어떻게 직렬화 대상에서 제외시켰을까?](#3-transient-어떻게-직렬화-대상에서-제외시켰을까)
4. [결론](#4-결론)

### 1. 직렬화와 역직렬화

- 직렬화(Serialization)
    - JVM 메모리에 있는 객체를 바이트 스트림으로 변환하는 작업
- 역직렬화 (Deserialization)
    - 직렬화한 바이트 스트림을 다시 자바객체로 변환하는 것

### 2. transient란?

- 객체를 저장하거나, 다른 JVM으로 보낼 때, transient라는 예약어를 사용하여 선언한 변수는 Serializable의 대상에서 제외된다.
- 즉, 해당 객체는 저장 대상에서 제외되어 버린다.

아래와 같은 코드를 예로 들어보자.

- 직렬화 코드
    
    ```java
    private void saveObject() {
            Person person = new Person("jammini", 20, "password");
            FileOutputStream fos = null;
            ObjectOutputStream oos = null;
            try {
                fos = new FileOutputStream("test.out");
                oos = new ObjectOutputStream(fos);
                oos.writeObject(person);
                System.out.println("Write Success");
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (oos != null) {
                    try {
                        oos.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
                if (fos != null) {
                    try {
                        fos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    ```
    
- 역직렬화 코드
    
    ```java
    private void loadObject() {
            FileInputStream fis = null;
            ObjectInputStream ois = null;
    
            try {
                fis = new FileInputStream("test.out");
                ois = new ObjectInputStream(fis);
                Object obj = ois.readObject();
                Person person = (Person) obj;
                System.out.println("person" + person);
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                if (ois != null) {
                    try {
                        ois.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
                if (fis != null) {
                    try {
                        fis.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    ```
    

```java
public class Person implements Serializable {
    private String name;
    private int age;
    private transient String password; // 직렬화에서 제외됨

    public Person(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", password='" + password + '\'' +
                '}';
    }
}
```

- 위의 Person 클래스를 생성하고 직렬화하고 역직렬화 한 경우 두가지를 살펴보자.
1. password 변수에 transient 키워드 사용X 경우 결과
    - personPerson{name='jammini', age=20, password='password'}
2. password 변수에 transient 키워드 사용O 경우 결과
    - personPerson{name='jammini', age=20, password='null'}

**이처럼 패스워드 변수와 같이 보안상 큰 문제가 발생할 수 있는 것들, 꼭 저장해야 할 필요가 없는 변수에 대해 transient를 사용 할 수 있다.**

### 3. transient 어떻게 직렬화 대상에서 제외시켰을까?

<img width="669" alt="image" src="https://user-images.githubusercontent.com/59176149/227952980-52160a72-52f7-4988-acd2-7067a536f417.png">


- ObjectOutputStream에 writeObject 메서드를 쭉 타고 들어가오면 위와 같이 getDefaultSerialFields를 보자.
- clFields배열을 보면 Person에 (name, age, password) 이렇게 3가지를 가지고 있는 것을 볼 수 있다.
- 그러나 반복문을 돌면서 필드중 STATIC이나 TRANSIENT가 아닌 경우에만 list에 담고 리턴한다.
- 즉, static 키워드와 transient 키워드를 사용하면 직렬화 대상에서 제외되는 것이다.

### 4. 결론

- transient 키워드를 사용하면 직렬화 대상에서 제외된다.
- 패스워드 변수와 같이 보안상 큰 문제가 발생할 수 있는 것들, 꼭 저장해야 할 필요가 없는 변수에 대해 transient를 사용하면된다.

### 참고

- 자바의 신 VOL.2 11장
- [https://nesoy.github.io/articles/2018-06/Java-transient](https://nesoy.github.io/articles/2018-06/Java-transient)
