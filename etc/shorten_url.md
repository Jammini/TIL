# 단축 URL을 사용하는 이유는 무엇이고 구현해보자

### 목차

1. [개요](#1-개요)
2. [나의 경험](#2-나의-경험)
3. [그래서 단축 URL에 장점이 뭔데?](#3-그래서-단축-url에-장점이-뭔데)
4. [구현하기](#4-1-구현하기-1차)
   4-1. [구현하기1](#4-1-구현하기-1차)
   4-2. [구현하기2](#4-2-구현하기-2차)
   4-3. [구현하기3](#4-3-구현하기-3차)
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

### 4-1. 구현하기 1차

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

### 4-2. 구현하기 2차

<img width="700" alt="Image" src="https://github.com/user-attachments/assets/9539e551-b912-444e-96d9-e8bb2ec4abdf" />

4-1 구현을 하고 코드리뷰를 받은 결과, 위 사진과 같은 리뷰가 달렸다. 겹칠 확률을 생각해 예외를 내보내고 있지만 더 좋은 방법이 있는지 생각해보았다.

처음 드는 생각으로는 unique한 결과를 내려면 shorturl을 만드는 **시간을 이용해 이것을 encode하면 되겠다는 생각을했다.**
하지만 0.0001초라도 동시에 수많은 요청이 온다고 한다면 같은 시간을 encode 할 수도 있을것이다. 그러면 unique한 값이면서 간단한게 무엇이 있을까?

**바로 id 값을 이용하는 것이다.**

id 값이 auto increament되기 때문에 이 id 값을 encode해서 내보낸다면 중복된 key값이 줄어들 것이라 판단했다.

1. 데이터 클래스 정의

데이터 클래스는 동일하게 진행하였다.

| orginalUrl | 원래 URL |
| --- | --- |
| shortenUrlKey | 단축 URL Key |
| redirectCount | 리다이텍트 카운트 |

2. 키 생성 알고리즘 구현

```java
public class DefaultBase62 {

    private static final String BASE62 = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    private static final int BASE = BASE62.length();

    @Override
    public String encode(long num) {
        StringBuilder encoded = new StringBuilder();
        while (num > 0) {
            encoded.append(BASE62.charAt((int) (num % BASE)));
            num /= BASE;
        }
        return encoded.reverse().toString();
    }

    @Override
    public long decode(String base62) {
        long decoded = 0;
        for (int i = 0; i < base62.length(); i++) {
            decoded = decoded * BASE + BASE62.indexOf(base62.charAt(i));
        }
        return decoded;
    }
}
```

id 값을 Base62로 변경한다. 현재는 id값을 단순히 Base62로 encode하지만 유튜브나 네이버와 같이 일관된 단축 URL 키를 만들고 싶다면 문자를 추가해 형태를 맞추면 될 것이다.

3. 플로우

<img width="697" alt="Image" src="https://github.com/user-attachments/assets/f02cfb5b-0a94-4649-97e0-576e46dac09c" />

플로우는 다음과 같다. 첫번째 플로우와 가장 큰 차이는 바로 Redis 캐시를 이용했다.

처음에는 단축 URL이 들어왔을때 중복된shortUrlKey 값이 존재하는지 DB를 통해 조회하였다.

하지만, URL이 1억개 있다고 하면 정상적으로 동작 할까? DB에 과부하가 걸려 성능에 굉장히 좋지 못할것이다. 그것을 가정하고 Redis 캐시를 이용하였다.

또한, redirectCount도 마찬가지로 DB를 통해 바로 count를 올리는게 아니고 스케줄러를 이용해 시간에 맞춰 count 값을 더해주어 저장해주었다.

정리하자면 다음과 같다.

1. **캐시 조회**
    - `cache.getByOriginalUrl(originalUrl)` 로 먼저 Redis에서 확인하여 있으면 바로 반환
2. **DB 조회**
    - 캐시에 없을 때만 DB를 호출해 조회
    - DB에 있으면 캐시를 채우고 반환
3. **신규 생성**
    - DB에 완전히 없는 URL이면 ID 기반 키 생성 후 엔티티에 저장
    - 생성된 키와 원본 URL을 캐시에 양방향으로 저장

그렇다면 이렇게 하면 **장점은 무엇일까?**

대다수의 재요청을 캐시를 통해서만 확인하기에 성능적으로 매우 우수한다. 왜냐하면 DB 조회를 할 필요 없기 때문이다.

즉, 캐시 미스 시에만 DB를 타므로 전체 성능이 크게 올라갈 것이다.

### 4-3. 구현하기 3차

위에 2가지 방법으로 구현했을때 보다 더 좋은 방법은 없을까?

위에 구현방법에 대해 문제점은 다음과 같다.

만약, 1억건의 데이터가 DB에 쌓이게 되었다고 가정하자.

<img width="672" alt="Image" src="https://github.com/user-attachments/assets/eb10679d-041b-4d72-9e80-ea1f37e36510" />

“www.youtube.com” 으로 단축 url 요청이 들어오면 originalUrl에 중복된게 없는지 첫번째부터 1억번째까지 Select 문을 통해 Full Scan이 일어날 것이다.

Full Scan하고 존재하지 않는다면 insert가 일어난다.

즉, originalUrl을 select 1번 insert 1번 이렇게 쿼리가 2번이 발생한다.

이러한 문제점을 파악하고 shortUrlKey 값을 이용하는게 아니라 id 값 자체를 key값으로 사용하는 방법을 구현하기3 에서 이용하였다.

해당 id칼럼을 이용해 base62로 encode/decode를 하는 것이다.

<img width="510" alt="Image" src="https://github.com/user-attachments/assets/b4553e0b-9169-495d-bc41-e71d61bab06b" />

그렇다면 구현하기2와 같이 2번의 쿼리(select + insert)가 아니라 1번의 쿼리(insert)만으로 처리가 된다.

그렇다면 해당 key값이 겹칠 확률도 없고 플로우도 굉장히 단순해지는 것을 확인할 수 있다.

<img width="624" alt="Image" src="https://github.com/user-attachments/assets/d155418b-5a12-4e36-b36d-ffb65ae572e9" />

그렇다면 단점으로는 다음과 같은 사항이 발생 할 수 있다.

<img width="549" alt="Image" src="https://github.com/user-attachments/assets/630069ea-e0e7-4aab-825c-bb398117a6cb" />

중복된 originalUrl이 계속 생길 것이고 데이터의 양이 굉장히 많아 질것이다.

여기서 트레이드 오프를 고려해야 하는데, 데이터의 양이 적으면 2번의 쿼리가 성능상 문제 없겠지만 데이터의 양이 많이 쌓인다면 1번의 쿼리만으로 효과를 낼 수 있는 구현하기3 을 고려할 것이다.

그렇다면 “redirectCount는 어떻게 처리할 것인가?” 에 대한 이야기가 나올 수 있다. 그 경우에는 중복된 originalUrl을 count를 하던가 별도의 count를 할 수 있는 테이블을 만들면 해결할 수 있다.

이렇게 구현하기 3차를 끝냈는데, 자신이 구현하고 있는 상황과 트레이드 오프를 고려해가면 설계하면 좋을 것이다.

### 5. 결론

간단하게 단축 URL을 만들어 보았다.

단축 URL을 사용하면 단순한 링크 공유이상의 가치를 만들 수 있을 것으로 보여진다.

현재 나는 DB에 단순히 저장해 영구적으로 단축 URL을 사용하지만 보안에 민감한 링크라면 만료기간을 설정한다던지 아님 추가적으로 누가 언제 어디서 몇번이나 클릭했는지 로그를 저장해 사용자들의 반응을 체크할 수도 있을 것이다.

보안성과 마케팅적으로 단축URL이 필요하다면 상황에 맞게 클래스나 기능들을 추가한다면 더 유용하게 단축 URL을 사용 할 수 있을 것이다.
