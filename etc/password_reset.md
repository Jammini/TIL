# 비밀번호 재설정링크로 이메일 전송 기능 구현하기

### 목차

1. 개요
2. 비밀번호 찾기 설계
3. 토큰 설계 (Redis vs DB)
4. 구현
5. 결론

### 1. 개요

이메일이나 SNS 계정 로그인 같은 방식이 현재는 많이 대중화되어 있지만, 아직도 많은 서비스가 자체 계정 시스템을 사용하고 있으며, 사용자는 종종 비밀번호를 잊어버린다.

이때 재설정 기능이 없다면 사용자는 로그인을 포기하거나 서비스를 이탈할 확률이 있다.

비밀번호 재설정 기능은 다양한 사이트에서 여러가지 제안을 제시한다.

그 중 제 프로젝트의 경우 이메일을 이용해 ID를 만들기 있으므로 이메일인증을 통해 재설정 하는 기능을 만들어보려고 한다.

### 2. 비밀번호 찾기 설계

1. 사용자가 이메일 입력 후 **비밀번호 재설정 요청**
2. 서버가 **1회용 토큰**을 생성해 저장
3. 토큰이 포함된 **재설정 링크**를 이메일로 발송
4. 사용자가 링크 클릭 → **새 비밀번호 입력** 화면으로 이동
5. 서버가 토큰 검증 후 **비밀번호 변경** 완료

<img width="647" alt="Image" src="https://github.com/user-attachments/assets/d521a616-3e20-470c-b3c8-f13eae883b73" />

### 3. 토큰 설계 (Redis vs DB)

| 방식 | 장점 | 단점 |
| --- | --- | --- |
| DB | 감사·리보크 용이 | 테이블 증가·I/O 부담 |
| Redis | 속도 우수·TTL 자동 만료 | 유실 시 복구 어려움 |

나는 이 중 Redis를 선택하였고 다음과 같은 이유로 선택하였다.

1. **성능 및 확장성**
    - Redis는 메모리 기반 키·값 저장소이기 때문에, DB에 비해 읽기·쓰기 속도가 매우 빠르다.
    - 비밀번호 재설정 같은 “발급 → 검증 → 삭제” 패턴이 매우 빈번히 일어나므로, 딜레이 없이 응답을 주기 위해 인메모리 저장소가 유리하다.
2. **TTL(자동 만료) 관리 편의성**
    - Redis는 TTL을 이용하면 저장한 값을 지정된 시간 후에 자동으로 삭제를 해준다.
    - DB를 쓰게되면 별도의 스케줄러로 만료된 레코드를 지우거나, 애플리케이션 레벨에서 만료 로직을 추가해야 하지만, Redis는 이 과정을 완전히 자동화해준다.
3. **1회용 토큰 보장 & 운영 부하 절감**
    - 비밀번호 재설정 토큰은 “한 번만 쓰이고 바로 무효화”되어야 하는데, Redis에서는 `GET` 후 `DEL`을 단일 트랜잭션으로 처리할 수 있다.
    - DB를 이용한다면 I/O 부담과 별도의 테이블을 만들어야 하는 문제가 있고, 만료 레코드 정리 작업이 별도로 필요하게 된다.

### 4. 구현

**비밀번호 재설정 요청**

```java
public PasswordResetResponse requestPasswordReset(String email) {
    User user = userService.getUserByEmail(email);

    // 토큰 생성
    String token = UUID.randomUUID().toString();
    String redisKey = passwordResetProperties.getRedisPrefix() + token;

    // Redis에 저장
    redisTemplate.opsForValue().set(redisKey, user.getId().toString(), passwordResetProperties.getExpiryMinutes());

    // 이메일 전송
    emailService.sendEmail(user.getEmail(), token, passwordResetProperties.getResetUrl(), passwordResetProperties.getExpiryMinutes());
    return new PasswordResetResponse(user.getEmail(), passwordResetProperties.getExpiryMinutes());
}
```

**비밀번호 재설정 확인**

```java
@Transactional
public void confirmPasswordReset(String token, String newPassword) {
    String redisKey = passwordResetProperties.getRedisPrefix() + token;

    // 토큰 찾기
    String userIdStr = redisTemplate.opsForValue().get(redisKey);
    if (userIdStr == null) {
        throw new InvalidTokenException();
    }

    // 비밀번호 변경
    Long userId = Long.valueOf(userIdStr);
    userService.changePassword(userId, newPassword);

    // 일회용 토큰 삭제
    redisTemplate.delete(redisKey);
}
```

**테스트**

<img width="605" alt="Image" src="https://github.com/user-attachments/assets/8193c599-7f6e-43f6-97e0-69a8b0dac18b" />

유저는 비밀번호를 분실하여 해당 이메일로 재설정 링크를 전달요청한다.

<img width="659" alt="Image" src="https://github.com/user-attachments/assets/f2edb75c-8d4b-44be-a84b-1a4b93a250b0" />

메일로 해당 메일을 받았으면 인증은 되었다고 판단한다.

<img width="655" alt="Image" src="https://github.com/user-attachments/assets/690ce50d-df35-42b4-88b0-a9fcb758b354" />

받은 메일의 URL은 30분간만 유효하고 해당링크 클릭하여 재설정 요청을 한다.

<img width="604" alt="Image" src="https://github.com/user-attachments/assets/417e71a5-4e06-4b5a-87c0-3d6af60bd5ea" />

비밀번호 재설정 링크도 들어왔다면 새비밀번호와 비밀번호 확인이 일치하는지 확인한 후 비밀번호를 변경한다.

이후 바뀐 비밀번호로 로그인을 하면 정상적으로 비밀번재설정이 된 것을 확인할 수 있다.

### 5. 결론

다음과 같이 비밀번호를 재설정하는 기능을 간단하게 구현하였다.

재설정하는 링크를 만들기 위해선 토큰이 필요한데, 토큰을 어디다가 저장해야할지 먼저 고민했었다.

먼저, 토큰 저장하는 부분에서 DB나 Redis 중 어떤 것을 이용할지 고민을 했다.

각각의 장단점을 파악한 후 “발급 → 검증 → 삭제” 패턴이 많이 발생하므로 DB I/O보다 Redis를 이용하는게 성능적으로 좋다고 판단했다.

또한, 결정적으로는 비밀번호 찾기는 자주 일어나는 요청이 아닐 것이라 판단했고, 순간 데이터가 유실되었다고 하더라고 재요청을 시도할 것으로 생각한다.

이러한 이유로, **“빠른 응답” + “TTL·1회용 관리 간편” + “운영 부하 최소화”** 를 모두 만족하는 Redis를 비밀번호 재설정 토큰 저장소로 선택하게 되었다.
