# JOIN의 종류(INNER, OUTER, CROSS, SELF JOIN)

### 목차

1. [JOIN 이란?](#1-join-이란)
2. [INNER JOIN(내부 조인)](#2-inner-join내부-조인)
3. [OUTER JOIN(외부 조인)](#3-outer-join외부-조인)
    
    3-1. [LEFT OUTER JOIN](#3-1-left-outer-join)
    
    3-2. [RIGHT OUTER JOIN](#3-2-right-outer-join)
    
    3-3. [FULL OUTER JOIN](#3-3-full-outer-join)
    
4. [CROSS JOIN(상호 조인)](#4-cross-join상호-조인)
5. [SELF JOIN(자체 조인)](#5-self-join자체-조인)
6. [결론](#6-결론)

## 1. JOIN 이란?

- 조인은 두 개의 테이블을 서로 묶어서 하나의 결과를 만들어 내는 것을 말한다.
- 두 테이블의 조인을 위해서는 기본키(PRIMARY KEY, PK)와 외래키(FOREIGN KEY, FK) 관계로 맺어져야 하고, 이를 **일대다 관계**라고 한다.

## 2. INNER JOIN(내부 조인)

- 두 테이블의 컬럼 값을 비교 후 서로 연관된 내용만 검색하는 조인이다.

<img width="462" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f356036f-580b-4eaf-89d6-fb1fa28e7a03">

## 3. OUTER JOIN(외부 조인)

- 두 테이블 사이에 일치 하지 않는 행도 포함하여 기준이 되는 테이블을 반환하는 조인이다.

<img width="529" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/84eba0f6-bebc-49e1-a73c-69b7a852e378">

### 3-1. LEFT OUTER JOIN

- 왼쪽 테이블의 모든 행과 오른쪽 테이블에서 일치하는 행을 반환한다. 오른쪽 테이블에서 일치하는 행이 없는 경우 NULL 값으로 채워진다.

### 3-2. RIGHT OUTER JOIN

- 오른쪽 테이블의 모든 행과 왼쪽 테이블에서 일치하는 행을 반환한다. 왼쪽 테이블에서 일치하는 행이 없는 경우 NULL 값으로 채워진다.

### 3-3. FULL OUTER JOIN

- 양쪽 테이블의 모든 행을 반환한다. 양쪽 테이블에서 일치하는 행이 없는 경우 NULL 값으로 채워진다.

## 4. CROSS JOIN(상호 조인)

- Cartesian Product(카디션 곱)이라고도 하며 한쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인시키는 기능이다. 상호 조인 결과의 전체 행 개수는 두 테이블의 각 행의 개수를 곱한 수만큼 된다.

<img width="588" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/8f6ff663-7a9c-49db-9a88-41d4682f6173">

## 5. SELF JOIN(자체 조인)

- SELF JOIN은 테이블에서 자기자신을 조인을 시키는 것이다.

<img width="487" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/84c289e3-adc0-4ded-ab22-05677fa6dc0c">

## 6. 결론

- 조인은 두 개의 테이블을 서로 묶어서 하나의 결과를 만들어 내는 것을 말한다.
- **INNER JOIN(내부 조인)**은 두 테이블을 조인할 때, 두 테이블에 모두 지정한 열의 데이터가 있어야 한다.
- **OUTER JOIN(외부 조인)**은 두 테이블을 조인할 때, 1개의 테이블에만 데이터가 있어도 결과가 나온다.
- **CROSS JOIN(상호 조인)**은 한쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인하는 기능이다.
- **SELF JOIN(자체 조인)**은 자신이 자신과 조인한다는 의미로, 1개의 테이블을 사용한다.
- 성능적으로보면 INNER JOIN이 OUTER JOIN보다 교집합만 조회함으로써 더 빠르다. 주로 INNTER JOIN을 많이 사용하며 그렇다고 OUTER JOIN이 안좋다는 것은 아니니 상황에 맞게 잘 사용하도록 해야한다.

### 참고

- [https://hongong.hanbit.co.kr/sql-기본-문법-joininner-outer-cross-self-join/](https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/)
