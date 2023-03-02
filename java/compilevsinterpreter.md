# 컴파일언어 vs 인터프리터언어 자바의 정체가 무엇인가?

## 목차

1. [컴파일언어](https://www.notion.so/vs-54cae480029a4162a7aafd6c9cb5196e)
2. [인터프린터언어](https://www.notion.so/vs-54cae480029a4162a7aafd6c9cb5196e)
3. [자바는 왜 두개의 고급언어를 가지고 있는건가?](https://www.notion.so/vs-54cae480029a4162a7aafd6c9cb5196e)
4. [JIT 컴파일러](https://www.notion.so/vs-54cae480029a4162a7aafd6c9cb5196e)

## 1. 컴파일언어

<img width="592" alt="image" src="https://user-images.githubusercontent.com/59176149/222390068-bc6b4248-3469-4371-8589-0db50b761b58.png">


- 소스 코드를 작성한다.
- 컴파일 프로그램을 이용하여 컴파일한다.
- 실행파일이 만들어진다.
- 실행 파일을 실행시킨다.
- 속도가 빠르다
- **Java,** C++, Visual C++, Object C 등이 있다.

## 2. 인터프리터언어

<img width="601" alt="image" src="https://user-images.githubusercontent.com/59176149/222390191-c8460957-6d85-4bac-889c-7a237c08380a.png">


- 소스 파일을 해석 엔진 프로그램(interpreter)을 이용해 소스를 한 줄 씩 실행한다.
- 실행파일이 만들어지지 않는다.
- 속도가 느리다.
- **Java**, 자바스크립트 등이 있다.

## 3. 자바는 왜 두개의 고급언어를 가지고 있는건가?

<img width="627" alt="image" src="https://user-images.githubusercontent.com/59176149/222390296-82fba171-d728-4ebc-8a0b-d421df1f366d.png">

- JVM이 클래스의 바이트코드를 운영체제에서 실행 할 수 있는 2진코드로 변경한다.
    - 즉, 각각의 **운영체제 환경에 맞게 CPU가 읽을 수 있도록 변환**해준다.
- 위 기능이 가능하다는건 **비용이 적게 든다**는 것과 같다.
    - 비용이 적게 든다는 것은 상당한 메리트이자 자바를 많이 쓰는 이유가 된다.

## 4. JIT 컴파일러

- JIT에 대한 JVM구조를 자세히 알아보고 싶다면 [링크](https://github.com/Jammini/TIL/blob/master/java/%EB%8D%94-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%A1%B0%EC%9E%91%ED%95%98%EB%8A%94-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%B0%A9%EB%B2%95.md#6-jvm-%EA%B5%AC%EC%A1%B0)를 타고 확인하길 바란다.
- 간단히 말하자면, JIT 컴파일러는 인터프리터 언어에 단점(속도 측면에서의 이슈)을 보완하고자 하였는데 기본적으로 자주 읽게 되는 소스에 대해서 메모리에 해당 부분을 캐싱해두고 이를 읽어야 하는 시점에 덩어리째로 반환을 해주는 방식을 사용했다. 이러한 부분 이용해 Java는 속도에 대한 부분을 해결하고자 노력했다.

### 참고

- [https://rumor1993.tistory.com/90](https://rumor1993.tistory.com/90)
- [https://www.youtube.com/watch?v=ZPBTGk50Ixc](https://www.youtube.com/watch?v=ZPBTGk50Ixc)

