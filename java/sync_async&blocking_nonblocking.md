# Sync(동기)vs Async(비동기) / Blocking vs Non-blocking

### 목차

1. [동기 (Synchronous) & 비동기 (Asynchronous)](#1-동기-synchronous--비동기-asynchronous)
    
    1-1. [동기 (Synchronous)](#1-동기-synchronous)
    
    1-2. [비동기 (Asynchronous)](#2-비동기-asynchronous)
    
2. [블로킹 (Bloking) & 논블로킹 (Non-Bloking)](#2-블로킹-bloking--논블로킹-non-bloking)
    
    2-1. [블로킹 (Bloking)](#1-블로킹-bloking)
    
    2-2. [논블로킹 (Non-Bloking)](#2-논블로킹-non-bloking)
    
3. [동기&비동기 + 블로킹&논블로킹 비교](#3-동기비동기--블로킹논블로킹-비교)
    
    3-1. [Sync & Blocking](#1-sync--blocking)
    
    3-2. [Sync & Non-Blocking](#2-sync--non-blocking)
    
    3-3. [Async & Non-Blocking](#3-async--non-blocking)

    3-4. [Async & Blocking](#4-async--blocking)
    
4. [결론](#4-결론)

## 1. 동기 (Synchronous) & 비동기 (Asynchronous)

- 결과를 돌려주었을 때 순서와 결과에 관심이 있는지 아닌지로 판단할 수 있다.

### 1. 동기 (Synchronous)

- 현재 작업의 응답과 다음 작업의 요청의 타이밍을 동시에 맞추는 것
- 요청한 순서를 가지고 진행된다는 것

![image](https://user-images.githubusercontent.com/59176149/227540930-f6b1ab44-9b8c-464f-917d-1b0699e9c5de.png)

### 2. 비동기 (Asynchronous)

- 요청한 그 자리에서 결과를 주어지지 않음
- 작업을 지시하면 그 작업이 언제 끝나는 지는 신경쓰지 않는다.

![image](https://user-images.githubusercontent.com/59176149/227541004-774e41a0-8654-4da2-b82a-45a8d6930707.png)

## 2. 블로킹 (Bloking) & 논블로킹 (Non-Bloking)

- 다른 주체가 작업할 때 자신의 제어권이 있는지 없는지로 볼 수 있다.

### 1. 블로킹 (Bloking)

- A함수가 B함수를 호출하면 제어권을 A가 호출한 B함수에 넘겨줌.

![image](https://user-images.githubusercontent.com/59176149/227541160-7d274d51-7573-4c1b-a0bb-cca54dae5bf4.png)

1. A가 실행하다 B를 호출함.
2. 제어권을 B에게 넘겨줌
3. B함수를 실행함
4. B의 실행이 끝나면 A에게 다시 제어권을 넘겨줌
5. 다시 A함수 실행

### 2. 논블로킹 (Non-Bloking)

- A 함수가 B 함수를 호출해도 제어권은 그대로 자신이 가지고 있는다.

![image](https://user-images.githubusercontent.com/59176149/227541236-9ca2c4da-6a81-442d-9e30-44c9141b9232.png)

1. A가 B를 호출하지만, 제어권은 A가 그대로 가지고 있으며 계속 A함수가 실행된다.
2. 제어권은 받지 않고 B함수는 실행하고 종료된다.

## 3. ****동기&비동기 + 블로킹&논블로킹 비교****

### 1. Sync & Blocking

![image](https://user-images.githubusercontent.com/59176149/227541402-2d8e74d9-73a9-4c70-86e4-64b9fe06e9ed.png)

- 절차
    1. A가 실행하다 B를 호출함.
    2. 제어권을 B에게 넘겨줌
    3. B함수를 실행함
    4. B함수가 완료되면 A에게 다시 제어권을 넘겨줌
    5. 다시 A함수 실행
- 정리
    - 현재 작업의 응답과 다음 작업의 요청의 타이밍을 동시에 맞춤 (Sync)
    - 제어권을 B에게 넘겨주고 B가 실행을 완료하여 제어권을 돌려줄때까지 기다림 (Blocking)

### 2. Sync & Non-Blocking

![image](https://user-images.githubusercontent.com/59176149/227541455-624440b9-4763-415a-970e-473927c67ba6.png)

- 절차
    1. A가 실행하다 B를 호출함.
    2. 제어권은 A가 그대로 가지고 있고 B함수는 실행됨
    3. A가 실행되면서 B함수가 완료되었는지 중간중간 계속 체크함
    4. B함수가 끝났다고 A에게 알려주면서 응답 값이 존재하면 리턴해줌.
    5. 다시 A함수 실행
- 정리
    - A는 B가 끝나는 시간을 맞추기 위해 중간중간에 완료가 되었는지 계속 체크를 해서 끝나는 시간을 맞춤 (Sync)
    - A는 B에게 제어권을 주지 않고 자신의 코드를 계속 실행함 (Nonblocking)

### 3. Async & Non-Blocking

![image](https://user-images.githubusercontent.com/59176149/227541495-2ba6ba3f-ea46-4af2-b3a0-455570c2b188.png)

- 절차
    1. A는 실행중 B를 호출하고 계속 실행됨.
    2. 제어권은 A가 그대로 가지고 있고 B함수는 실행됨
    3. B의 작업이 끝나면 콜백을 실행함.
- 정리
    - A는 B에게 제어권을 주지 않고 계속 실행되는 상태 (Nonblocking)
    - A가 B를 호출할 때 콜백함수를 함께 주는데, B는 작업이 끝나면 A가 준 콜백함수를 실행함 (Async)

### 4. Async & Blocking

![image](https://user-images.githubusercontent.com/59176149/227541546-53869794-b1c7-4e05-b97d-563352c87bd3.png)

- 절차
    1. A가 실행하다 B를 호출함.
    2. 제어권을 B에게 넘겨줌
    3. B함수를 실행함
    4. B함수가 완료되면 콜백함수 실행하고 A에게 다시 제어권을 넘겨줌
    5. 다시 A함수 실행
- 정리
    - A는 B함수가 종료되든 말든 신경쓰지 않고, 콜백함수를 보냄 (Async)
    - A는 B함수의 작업에 관심 없음에도 A함수는 B에게 제어권을 넘김 (Blocking)

## 4. 결론

- 동기/비동기
    - 요청받은 함수가 작업을 완료했는지를 체크해서 순차적 흐름의 차이
    - 동기 : 요청자가 요청받은 함수의 작업이 완료되었는지 계속 확인 (함수들의 시간을 맞춤)
    - 비동기 : 요청자는 요청 후 신경쓰지 않음. 요청 받은 함수가 작업을 마치면 알려줌. (작업시간/종료시간 맞추지 않음)
- 블로킹/논블로킹
    - 요청받은 함수가 제어권을 계속 가지고 있는지 없는지에 대한 여부
    - 블로킹 : 요청된 함수가 작업을 끝내고 제어권을 다시 넘겨줌 (요청자는 대기)
    - 논블로킹 : 요청된 함수가 요청자에게 제어권을 바로 넘겨줌 (요청자는 자기 일을 계속함)

### 참고

- [https://www.youtube.com/watch?v=oEIoqGd-Sns&t=281s](https://www.youtube.com/watch?v=oEIoqGd-Sns&t=281s)
- [https://hesh1232.tistory.com/158](https://hesh1232.tistory.com/158)
- [https://evan-moon.github.io/2019/09/19/sync-async-blocking-non-blocking/#동기-방식--논블록킹-방식](https://evan-moon.github.io/2019/09/19/sync-async-blocking-non-blocking/#%EB%8F%99%EA%B8%B0-%EB%B0%A9%EC%8B%9D--%EB%85%BC%EB%B8%94%EB%A1%9D%ED%82%B9-%EB%B0%A9%EC%8B%9D)
- [https://velog.io/@nittre/블로킹-Vs.-논블로킹-동기-Vs.-비동기](https://velog.io/@nittre/%EB%B8%94%EB%A1%9C%ED%82%B9-Vs.-%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EB%8F%99%EA%B8%B0-Vs.-%EB%B9%84%EB%8F%99%EA%B8%B0)
