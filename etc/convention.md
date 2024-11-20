# [IntelliJ] 자바 구글 코딩 컨벤션 적용하기

### 목차

1. 개요
2. Google Java Style Guide 적용하기
3. 결론

### 1. 개요

여러 사람들과 효율적인 유지보수와 협업을 가능하기 위해 많은 프로젝트에서는 코딩 컨벤션을 적용한다.

코드의 일관적인 스타일을 유지하면서 누구든지 쉽게 이해할 수 있도록 가독성이 향상된다.

그래서, 이번에는 IntelliJ에서 자바 구글 코딩 컨벤션을 적용해보려고 한다.

### 2. Google Java Style Guide 적용하기

https://google.github.io/styleguide/javaguide.html

적용하기전, 먼저 위 링크에 들어가 코딩컨벤션이 어떻게 이루어져 있는지 확인해보면 좋을 것이다.

<img width="699" alt="image" src="https://github.com/user-attachments/assets/cfb41e37-39f1-43c2-bcf4-f7860a67484b">

https://github.com/google/styleguide

위 링크에서 intellij-java-google-style.xml 을 다운받는다.

<img width="706" alt="image" src="https://github.com/user-attachments/assets/66ad1f30-b80f-40e1-a487-ddc1552e7332">

File > Settings > Code Style로 들어가 상단의 톱니바퀴 모양을 누르고, Import Scheme > IntelliJ IDEA code style scheme을 선택한다. 그리고 다운로드 받았던 intellij-java-google-style.xml 을 선택한다.

<img width="702" alt="image" src="https://github.com/user-attachments/assets/62e75415-3f84-4cae-9b88-e29ea7b6972a">

Scheme에서 가져온 GoogleStyle을 적용한다. 처음 가져오면 디폴트로 Tap size와 Indent가 2로 설정되어 있는데 나는 2보다는 4가 익숙하기에 4로 변경하고 해당 사항을 적용하였다.

### 3. 결론

위와 같이 설정을 하였다면, 코드 스타일이 적용되는지 확인해보자.

맥 기준 ( Option + Command + L )윈도우 기준 ( Ctrl + Alt+ L ) 을 누르면 자동으로 코드 스타일이 정리가 될 것이다.