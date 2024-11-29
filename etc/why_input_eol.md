### 목차

1. [개요](#1-개요)
2. [EoL은 무엇인가?](#2-eol은-무엇인가)
3. [파일 마지막게 개행을 왜 해야할까?](#3-파일-마지막게-개행을-왜-해야할까)
4. [IDE 설정으로 해결하기](#4-ide-설정으로-해결하기)
5. [.editorconfig 파일로 해결하기](#5-editorconfig-파일로-해결하기)
6. [결론](#6-결론)

### 1. 개요

<img width="380" alt="image" src="https://github.com/user-attachments/assets/50fcb9b1-f661-48ce-8089-3156508f4037">

프로젝트 진행중에 코드리뷰를 받았던 내용 중 다음과 같이 EoL을 설정해주는게 좋다는 리뷰를 받았다.

예전부터 파일마다 개행을 넣어야 한다고는 들었으나, ‘왜 넣어야 하는지?’ 에 대한 의문만 가지고 관행적으로 넣었었다.

이번 기회를 통해 무심하게 생각했던 내용에 대해서 정리해보려고 한다.

### 2. EoL은 무엇인가?

EOL(End Of Line)은 “줄의 끝” 이라는 의미로 주로 프로그래밍 파일 형식에서 운영 체제에 따라 서로 다른 방식으로 표현되어진다.

### 3. 파일 마지막게 개행을 왜 해야할까?

이유는 [POSIX 기반의 규칙](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap03.html#tag_03_206)에 기반한다.

많은 프로그램이 이 규칙에 따라 구현되어 있으면 이를 위반 시 예기치 않은 동작이 일어 날 수 있다고 한다.

즉, 의도한대로 파일이 작동되는 것을 보장하기 위해선 자동으로 규칙을 정해서 관리하는 것을 추천한다.

해결하는 방법은 크게 2가지인데 아래 자세히 알아보자.

### 4. IDE 설정으로 해결하기

가장 간단한 방법이다. 항상 파일 끝에 newline 을 추가하도록 하는 것이다.

<img width="707" alt="image" src="https://github.com/user-attachments/assets/cd2aa7f1-135f-4048-8ff1-46bafe0e1d0c">

IDE > Settings > Editor > General 로 들어간다.

아래 쪽에 Ensure every saved file ends with a line break 를 체크해준다.

이제 Apply를 누르면 파일을 저장할때마다 항상 newline을 추가해준다.

### 5. .editorconfig 파일로 해결하기

IDE 설정은 프로젝트마다 개개인이 설정하는 반면에 .editorconfig 파일을 이용하면 프로젝트 관리하는데 있어서 편리하다.

개발환경이 분산되어 있거나 다른 사람들도 같이 개발하는 경우 매번 IDE 설정을 하기보다는 .editorconfig 로 하는 것은 권장한다.

editorconfig은 많은 개 발자들이 일정한 코드 스타일을 유지하기 위해 도와주는 설정 파일이다.

```jsx
# 모든 파일에 마지막 줄 삽입
[*]
insert_final_newline = true
```

`.editorconfig` 파일에 다음과 같은 설정을 추가하여 EoF 동작을 정의할 수 있다.

<img width="705" alt="image" src="https://github.com/user-attachments/assets/45b94aba-00dd-4d1e-9683-09744e695e85">

파일 구성은 위처럼 쉽게 구현할 수 있다. 현재 나는 [Google Code Style Guides](https://github.com/google/styleguide)를 이용하고 있는데, 개행문자가 들어가 있지 않다.

위와 같이 내용을 추가해서 `.editorconfig`를 이용하여 파일을 배포한다면 여러 사람이 일관된 코드를 사용하는데 있어서 도움이 될 것이다.

### 6. 결론

- EOF 가 없으면 예기치 않은 동작이 일어 날 수 있다.
- 이유는 [POSIX 기반의 규칙](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap03.html#tag_03_206)에 기반한다.
- 의도한대로 파일이 작동되는 것을 보장하기 위해선 자동으로 규칙을 정해서 관리하는 것을 추천한다. (feat. IDE, .editorconfig)

### 참고

- https://jojoldu.tistory.com/673
- [https://velog.io/@jangws/EOL을-넣어야-하는-이유와-git에서-CRLF-EOL-차이로-인한-문제-해결-방법](https://velog.io/@jangws/EOL%EC%9D%84-%EB%84%A3%EC%96%B4%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EC%99%80-git%EC%97%90%EC%84%9C-CRLF-EOL-%EC%B0%A8%EC%9D%B4%EB%A1%9C-%EC%9D%B8%ED%95%9C-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95)
