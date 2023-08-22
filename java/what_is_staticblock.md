# static block 무엇이고 언제 사용할까?

### - static block이란?

- 객체는 여러 개를 생성하지만, 한 번만 호출되어야 하는 코드가 있다면 "static 블록"을 사용.
- 객체가 생성되기 전에 한 번만 호출.
- 클래스 내에 선언 O
- 메소드 내에 선언 X
- 클래스 초기화할 때 꼭 수행되어야 할 작업이 있을 경우 유용하게 사용됨.

### - 예시

```java
public class StaticBlock {
    static int data = 1;

    public StaticBlock() {
         System.out.println("StaticBlock Constructor.");
    }

     static { // static 블럭1
         System.out.println("*** First static block. ***");
         data=3;
     }

     static { // static 블럭2
         System.out.println("*** Second static block. ***");
         data=5;
     }

     public static int getData() {
          return data;
     }
}
```

```java
public void makeStaticBlock() {
    System.out.println("Creating block1");
    System.out.println(StaticBlock.data);
    StaticBlock block1 = new StaticBlock();
    System.out.println("Creating block1");
    System.out.println("-------------------");
    System.out.println("Creating block2");
    StaticBlock block2 = new StaticBlock();
    System.out.println("Creating block2");
}
```

아래 결과를 보다 시피, static 블록은 생성자가 불리지 않아도, 클래스에 대한 참조가 발생하자마자 호출되는 것을 볼 수 있다.

```java
Creating block1
*** First static block. ***
*** Second static block. ***
5
StaticBlock Constructor.
Creating block1
-------------------
Creating block2
StaticBlock Constructor.
Creating block2
```