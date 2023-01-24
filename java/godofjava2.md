# 자바의 신 VOL.2

**해당 내용은 자바의 신 VOL.2을 보고 내용 정리 하였습니다.**


## 20장 가장 많이 쓰는 패키지는 자바랭

### 1. 같은 패키지에 있는 클래스를 제외하고, 별도로 import 하지 않아도 되는 패키지는 무엇인가요?

- java.lang

### 2. 자바의 메모리가 부족해서 발생하는 에러는 무엇인가요?

- java.lang.OutOfMemoryError

### 3. 메소드 호출관계가 너무 많아서 발생하는 에러는 무엇인가요?

- StackOverflowError

### 4. java.lang 패키지에 선언되어 있는 3개의 어노테이션에는 어떤 것들이 있고, 각각의 역할은?

- Deprecated: 더 이상 사용되지 않음을 컴파일러에게 알림
- Override : 해당 메소드가 부모 클래스에 있는 메소드를 Override 했다는 것을 명시적으로 선언한다
- Suppress Warnings: 경고제외

### 5. Double과 Integer 같은 숫자 타입에서 처리할 수 있는 최대, 최소값을 알 수 있는 상수의 이름은?

- MIN_VALUE, MAX_VALUE

### 6. Integer값을 2진법으로 표현하려면 어떤 메소드를 사용해야 하나요?

- toBinaryString()

### 7. Integer값을 16진법으로 표현하려면 어떤 메소드를 사용해야 하나요?

- toHexString()

### 8. 속성(Properties)과 환경(Environment) 값의 차이는 무엇인가요?

- Properties는 java.util 패키지에 속하며 Hashtable의 상속을 받는다 Properties는 추가할 수도있고 변경 할수 있지만 환경값 env는 읽기만 가능하다

### 9. System.out과 System.err 에서 사용할 수 있는 메소드들은 어떤 클래스의 API를 봐야 하나요?

- PrintStream

### 10. System 클래스에서 현재 시간을 조회하는 용도로 사용하는 메소드 이름은 무엇인가요?

- System.currentTimeMillis()

### 11. System 클래스에서 시간 측정 용도로 사용하는 메소드 이름은 무엇인가요?

- System.nanoTime()

### 12. System.out.print() 메소드와 System.out.println() 메소드의 차이는 무엇인가요?

- 한줄띄기

### 13. System.out.println() 메소드에 객체가 매개변수로 넘어 왔을 때 String의 어떤 메소드가 호출되어 결과를 출력하나요? 그리고, 그 메소드를 사용하는 이유는 무엇인가요?

- String.valueOf()

### 14. 숫자 계산을 위해서 필요한 메소드들을 모아 놓은 클래스는 무엇인가요?

- Math

### 15. 위의 문제의 답인 클래스에 있는 메소드는 객체를 생성해서 사용해야 하나요?

- X

### 16. 숫자의 절대값을 구하는 메소드는 무엇인가요?

- abs

### 17. 숫자의 반올림을 하는 메소드는 무엇인가요?

- Math.round()

### 18. 각도를 라디안으로 변환하는 메소드와 라디안을 각도로 변환하는 메소드는 각각 무엇인가요?

- Math.toRadians() Math.toDegrees()

### 20. 5의 4 제곱 값을 구하려고 하면 어떤 메소드를 사용해야 하나요?

- Math.pow(밑, 지수)