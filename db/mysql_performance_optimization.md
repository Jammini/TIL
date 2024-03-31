# MySQL 성능 최적화
## Introduction

### 목표

- 성능 최적화 학습
- MySQL에 전반적인 이해
- 이해가 안되었던 내용이나 생각 공유

### 책 소개

<img width="293" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3f478fd6-1aa2-42d3-8165-0c6bd57f3674">

### 회차 방식

- 1주일에 1장 ~ 2장씩 읽고 정리 후 내용 공유

### 벌칙

- 스타벅스 4잔

## Presentation

### 1회차 - 2024/02/17

[1장 MySQL 아키텍쳐](https://github.com/Jammini/TIL/blob/master/db/mysql_architecture.md) 

### 2회차 - 2024/02/25

[2장 신뢰성 엔지니어링 환경에서의 모니터링](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization2.md)

[3장 성능 스키마](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization3.md)

### 3회차 - 2024/02/28

[4장 운영 체제 및 하드웨어 최적화](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization4.md)

### 4회차 - 2024/03/08

[5장 서버 설정 최적화](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization5.md)

[6장 스키마 설계와 관리](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization6.md)

### 5회차 - 2024/03/17

[7장 고성능을 위한 인덱싱](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization7.md)

[8장 쿼리 성능 최적화](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization8.md)

### 6회차 - 2024/03/22

[9장 복제](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization9.md)

[10장 백업 및 복구](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization10.md)

### 7회차 - 2024/03/29(마지막 회차)

[11장 MySQL 스케일링](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization11.md)

[12장 클라우드에서의 MySQL](https://github.com/Jammini/TIL/blob/master/db/mysql_performance_optimization12.md)

### 회고

스터디를 진행하면서 MySQL에 대해 전반적인 학습을 할 수 있었다. 아직 현업에서 MySQL을 사용해보지 못하여 이해 못한 내용들도 꽤나 많지만 스터디 하신 분들에게 궁금한 점을 물어보며 궁금증을 조금씩 해결해나갔다.

MySQL 또는 다른 데이터베이스를 사용하면서 데이터를 읽고 쓰고 수정하고만 생각했지 성능적으로 복제, 복구 등을 생각하지 못했었다. 설계를 할때나 데이터베이스를 사용할때 이런점을 꼭 고려해야 한다고 생각한다.

문제가 생기기전에 사전에 예방하는게 중요하다. 그런데도 불구하고 문제가 발생하면, 당면한 문제를 해결하기 위해 적합한 전략을 고민하고 해결해야한다. 모든 선택에 트레이드오프를 고려해가며 최적의 문제를 찾아내도록 하자.
