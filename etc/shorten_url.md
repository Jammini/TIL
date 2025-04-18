# 단축 URL을 사용하는 이유는 무엇이고 구현해보자

### 목차

1. [개요](#1-개요)
2. [나의 경험](#2-나의-경험)
3. [그래서 단축 URL에 장점이 뭔데?](#3-그래서-단축-url에-장점이-뭔데)
4. [구현하기](#4-구현하기)
5. [결론](#5-결론)

### 1. 개요

프로젝트를 진행하면서 게시글 공유하기 기능을 구현하던 중 단축 URL에 대한 이야기가 나왔다. 평소 유튜브나 네이버 블로그 등 공유하기 버튼을 누르면 길었던 URL 구조가 단축된 경우를 볼 수 있을 것이다.

평소에는 그냥 가볍게 지나왔는데 이번 프로젝트를 통해서 단축 URL을 왜 사용하고 구현하는 방법에 대해서 차근차근 정리해보려고 한다.

레츠고~

### 2. 나의 경험

먼저 나는 카카오톡 같이 URL 링크를 친구들과 대화방에서 주고 받을때 경험을 해봤다. 보통 PC 주소를 그대로 복사해서 전달할때 아래 쿠팡 링크 같이 길게 전달되는 경우가 종종 있었다.

<img width="235" alt="Image" src="https://github.com/user-attachments/assets/081f1bb4-2b47-4648-8764-5064e456a0ce" />

그래서 카톡 대화창이 길어지면 아래에 있는 전체보기 버튼이 활성화 되는데 전체보기 버튼을 한번 더 눌러 링크를 타고 갈 수 있게 된다.

만약 단축 URL을 이용하면 어떠할까?

<img width="238" alt="Image" src="https://github.com/user-attachments/assets/6fe25d41-b8d0-437f-b647-5ed4fb62856d" />

쿠팡에서 제공하는 공유하기 버튼을 눌러 카카오톡에 링크를 전달하면 아래와 같이 나온다.

<img width="378" alt="Image" src="https://github.com/user-attachments/assets/0357be52-8fe4-4f64-9e85-6660b942f6c5" />

즉, URL이 아까와 달리 매우 간결해졌고 카카오톡에서 이미지도 미리보기로 제공해주는 효과가 있다.

이것만으로도 단축 URL을 전달할때 효과가 굉장하다고 생각했다. 또한 모바일에서 PC로 큰 화면으로 보고 싶을때도 복사가 되지 않는 환경이라면 짧은 URL을 직접 쳐서 들어가기도 하였다.

### 3. 그래서 단축 URL에 장점이 뭔데?

단축 URL에 장점은 아래와 같다.

1. **가독성 향상 & 공유 편의성**

긴 URL은 복잡하고 보기 불편하다. 또한 모바일이나 SNS같은 메신저에 단축 URL이 훨씬 보기 좋고 공유하기도 쉬워진다.

```
❌ https://example.com/products/category/detail?id=123456789&ref=campaign&utm_source=...
✅ https://short.ly/Hr7StYBJ
```

2. **문자 수 제한 대응**

일부 플랫폼에서는 문자수를 제한할 가능성이 있다. 그래서 긴 URL을 가지고 있다면 잘리거나 깨질 가능성이 있다.

3. **동적 리다이렉션**

단축 URL은 리디렉션 기반이라, 기존 공유 링크를 바꾸지 않고도 대상 URL을 변경할 수 있는 잠점이 있다.

또한, 단축 URL을 통해 몇 번 redirect 가 되었는지 통계를 낼 수 있다.

### 4. 구현하기

먼저, 예시를 보자.

<img width="542" alt="Image" src="https://github.com/user-attachments/assets/93313524-2028-4a32-b4fd-e201e60ade8f" />

단축된 URL의 키는 8글자로 생성하고 해당 URL에서 “**L6b9WHLA**”를 키를 경로로 이용할 것이다.

1. 데이터 클래스 정의

| orginalUrl | 원래 URL |
| --- | --- |
| shortenUrlKey | 단축 URL Key |
| redirectCount | 리다이텍트 카운트 |

2. 키 생성 알고리즘 구현

키를 생성하는 알고리즘은 자유롭게 구현하면 되고 저는 아래와 같은 알고리즘을 이용했다.

```java
 public static String generateShortenUrlKey() {
    String base56Characters = "23456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnpqrstuvwxyz";
    Random random = new Random();
    StringBuilder shortenUrlKey = new StringBuilder();

    for(int count = 0; count < 8; count++) {
        int base56CharactersIndex = random.nextInt(0, base56Characters.length());
        char base56Character = base56Characters.charAt(base56CharactersIndex);
        shortenUrlKey.append(base56Character);
    }
    return shortenUrlKey.toString();
}
```

base56을 방식을 이용하였고 숫자로 표현할 수 있는 1이나 0이 빠져있고 대문자 I, O 등등 몇개가 빠져있는 것을 볼 수 있을것이다.

3. 플로우

<img width="551" alt="Image" src="https://github.com/user-attachments/assets/1dc525fd-9633-4bef-87ca-8763b9c48e16" />

사용자가 단축된 URL로 요청하면 원래의 URL로 리다이렉트(redircet)되어야 한다.

또한, 원래의 URL로 다시 단축 URL을 생성해도 항상 새로운 단축 URL이 생성되어야 하며 기존에 생성되었던 단축 URL도 여전히 동작되어야 한다.

### 5. 결론

간단하게 단축 URL을 만들어 보았다.

단축 URL을 사용하면 단순한 링크 공유이상의 가치를 만들 수 있을 것으로 보여진다.

현재 나는 DB에 단순히 저장해 영구적으로 단축 URL을 사용하지만 보안에 민감한 링크라면 만료기간을 설정한다던지 아님 추가적으로 누가 언제 어디서 몇번이나 클릭했는지 로그를 저장해 사용자들의 반응을 체크할 수도 있을 것이다.

보안성과 마케팅적으로 단축URL이 필요하다면 상황에 맞게 클래스나 기능들을 추가한다면 더 유용하게 단축 URL을 사용 할 수 있을 것이다.
