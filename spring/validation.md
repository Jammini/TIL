# Validation 책임과 범위는 어떻게 가져가야할까?

### 목차

1. [개요](#1-개요)
2. [Client vs Server](#2-client-vs-server)
3. [Layer별 책임](#3-1-controller-layer)
    
    3-1. [Controller Layer](#3-1-controller-layer)
    
    3-2. [Service Layer](#3-2-service-layer1)
    
    3-3. [Repository Layer](#3-3-repository-layer)
    
4. [결론](#4-결론)

## 1. 개요

개인 프로젝트를 진행하다 보니 작은 고민이 생겼다.

과연 `어떤 Layer에서 Validation을 체크하는게 맞을까?` 에 대한 고민이었다.

크게 Controller, Service, Repository로 나누었다면 넘어온 값들에 대한 벨리데이션에 대한 책임을 나누어야 한다고 생각했다.

## 2. Client vs Server

**Client**

- 빠르게 검증은 가능하지만 Client에서는 조작이 가능하기 때문에 Client만 검증을 하는 것은 안된다.

**Server**

- 빠르게 검증은 불가능하지만 안정적인 유효성 검사가 가능하고 DB를 통한 검증도 가능하다.

Client, Server 둘 다 검증하면 Best지만, 둘 중 하나에서만 검증 해야 한다면 Server에서 해야 한다.

## 3. Layer별 책임

서버에서 각각의 Layer에 적절한 유효성 검사를 위치 시켜 책임을 분산 시켜야 한다.

정답은 없고 각 Layer에 대한 책임에 대한 기준이 존재해야지 일관성 있는 책임을 유지 할 수 있다.

### 3-1. Controller Layer

Client에서 Input을 받아 Service Layer로 전달하고, Service Layer가 반환하는 데이터를 Client에 전달하는 것이 목적이다. 

그렇기 때문에 받은 데이터가 내가 예상했던 형식(shape)인지 Type은 맞는지, 필수 데이터가 존재하는지에 대한 내용에 중점을 두었다.

아래 Product 데이터 형식을 예를 들어 살펴보자.

```json
{
	"name": "나이키 슈즈",
	"sellingStatus": "SELLING",
	"quickPrice": "20000",
	"startedAt": "2023-12-18T05:44:37",
	"userId": "1"
}
```

1. 유효한 Json data형식으로 들어왔나?
2. name는 String 이며 @NotBlank(Null, “”, “ ”) 형식인가?
3. sellingStatus은 String 형식이며 @NotNull 형식인가?
4. quickPrice는 int이며 @Positive 0을 초과한 형식인가?
5. startedAt가 LocaldateTime 형식이면서 패턴에 맞는 형식인가?
6. userId가 Long이며 @Notnull형식인가?

### 3-2. Service Layer

Controller에서 받은 Input을 받아 기획에 따른 비즈니스 로직을 수행한다.

그렇기 때문에 비지니스적인 검증에 대해 중점을 두었다.

Service 레이어에서 적절성의 유무는 사용자의 Input의 type과 shape이 아닌 비즈니스 로직이기 때문에 Validation이 좀 더 복잡하다.

```java
@Override
public boolean isValid(Enum value, ConstraintValidatorContext context) {
    boolean result = false;
    Object[] enumValues = this.annotation.enumClass().getEnumConstants();
    if (enumValues != null) {
        for (Object enumValue : enumValues) {
            if (value == enumValue) {
                result = true;
                break;
            }
        }
    }
    return result;
}
```

sellingStatus는 여러 String 값을 가질 수 있지만 비지니스적으로 `SELLING("판매중"), HOLD("판매대기"), STOP_SELLING("판매중지")` 이중에 하나여야 한다면 Service Layer 에서 validatio을 잡아주었다.

### 3-3. Repository Layer

Repository는 Service layer에서 받은 입력값을 통해 Database에 액세스하는 것을 목적으로 하는 Interface다.

 사실상 Service layer에서 쿼리에 필요한 data를 어느 정도 검증 후 Repository의 인자로 전달하고 ORM( sequelize)에서 어느 정도의 Validation을 수행하게 된다. 

입력값의 개수를 Repository에서 확인해야 하는 경우가 아니라면 추가적인 Validation을 필요로 하지 않다.

## 4. 결론

프로젝트를 진행하면서 고민되었던 부분중 하나가 각각의 Layer에서 Valdation에 대한 책임을 어디까지 가져야하나에 대한 고민이 있었습니다.

이번 내용을 정리하면서 적절한 Validation에 대한 기준과 범위를 고민을 해볼 수 있었다.

사실 정답은 없기에 프로젝트를 진행하면서 여러 사람들의 의견도 듣고 나만의 기준을 좀 더 세밀하게 잡아보려 한다.

### 참고

- https://blog.frankdejonge.nl/where-does-validation-live/
- [https://blog.barogo.io/개발인턴-validation의-적절성-79ed23f28a72](https://blog.barogo.io/%EA%B0%9C%EB%B0%9C%EC%9D%B8%ED%84%B4-validation%EC%9D%98-%EC%A0%81%EC%A0%88%EC%84%B1-79ed23f28a72)