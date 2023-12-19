# @NotNull @NotBlank @NotEmpty를 뭐가 다를까?

### 목차

1. [개요](#1-개요1)
2. @[NotNull 이란?](#2-notnull-이란)
3. [@NotEmpty 란?](#3-notempty란)
4. [@NotBlank 란?](#4-notblank란1)
5. [결론](#5-결론)

### 1. 개요

프로젝트를 진행하면서 validation체크를 하기 위한 비슷한 (@NotNull, @NotBlank, @NotEmpty) 3가지 어노테이션을 마주하게 되었다.

스프링 부트 3.0부터는 기존 `javax.validation.constraints` 에서 `jakarta.validation.constraints` 로 패키지명이 변경 되었으나 동일한 기능을 제공하고 비슷한 어노테이션에 차이를 알아보려고 한다.

### 2. @NotNull 이란?

![image](https://github.com/Jammini/TIL/assets/59176149/4ce5a19a-93f2-40a6-9702-c8b660e7946d)

**The annotated element must not be null. Accepts any type.**

- @NotNull은 Null 값만 허용하지 않는다. 즉 Null이 아닌 “”, “ “ 인 경우에는 허용을 한다.

### 3. @NotEmpty란?

![image](https://github.com/Jammini/TIL/assets/59176149/191a66c9-17e9-48a0-a944-e0b9293bab09)

**The annotatd element must not be null nor empty.**

**Supported types are :**

1. **CharSequence (length of character sequence is evaluated)**
2. **Collection (collection size is evaluated)**
3. **Map (map size is evaluated)**
4. **Array (array length is evaluated)**
- @NotEmpty는 null과 “” 도 허용하지 않는다. 하지만 “ “ 의 입력은 허용된다.

### 4. @NotBlank란?

![image](https://github.com/Jammini/TIL/assets/59176149/11ea7746-55b2-4bf2-bf44-da29b71c250e)


**The annotated element must not be null and must contain at least one non-whitespace character. Accepts CharSequence.**

- @NotBlank는 null, “”, “ “ 모두 허용하지 않는다.

### 5. 결론

- 내용을 간단하게 표로 정리하면 아래와 같다. 주어진 요구상황에 맞게 validation을 사용하자.

|  | null | “” | “ “ |
| --- | --- | --- | --- |
| @NutNull | false | true | true |
| @NotEmpty | false | false | true |
| @NotBlank | false | false | false |
