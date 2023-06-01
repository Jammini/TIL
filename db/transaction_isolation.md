# 트랜잭션 Isolation level

1. [트랜잭션 동시 실행시 이상 현상](#1-트랜잭션-동시-실행시-이상현상)
    
    1-1. [Dirty read](#1-dirty-read)
    
    1-2. [Non-repeatable read](#2-non-repeatable-read)
    
    1-3. [Phantom read](#3-phantom-read)

2. [Isolation level](#2-isolation-level)
    
    2-1. [READ UNCOMMITTED](#1-read-uncommitted)
    
    2-2. [READ COMMITTED](#2-read-committed)
    
    2-2-1. [NON-REPEATABLE READ 발생](#2-1-non-repeatable-read-발생)
    
    2-3. [REPEATABLE READ](#3-repeatable-read)
    
    2-3-1. [PHANTOM READ 발생](#3-1-phantom-read-발생)
    
    2-4. [SERIALIZABLE](#4-serializable)
    
3. [결론](#3-결론)

## 1. 트랜잭션 동시 실행시 이상현상

트랜잭션들이 동시에 실행될때 발생 가능한 이상 현상들은 다음과 같다.

1. Dirty read
2. Non-repeatable read
3. phantom read

이런 이상한 현상들이 모두 발생하지 않게 만들 수 있지만 그러면 제약사항이 많아져서 동시 처리 가능한 트랜잭션 수가 줄어들어 결국 DB의 전체 처리량이 하락하면서 성능이 안좋아진다.

그러므로, 위와 같이 일부 이상한 현상은 허용하는 몇가지 level을 만들어서 사용자가 필요에 따라서 적절하게 선택할 수 있도록 하는 것이 바로 Isolation level이 된다.

## 1. Dirty read

- commit 되지 않은 변화를 읽었을때 이상한 현상

<img width="703" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a736cc1e-8d04-4680-85a9-e594314f80e9">

- 트랜잭션1
    - x에 y를 더한다.
- 트랜잭션2
    - y를 70으로 바꾼다.

트랜잭션1이 x에 y를 더하기 위해 x값 10을 읽고 y값을 더하기 전에 트랜잭션2가 y를 70으로 바꿨다.

트랜잭션이 y값 70을 읽고 x값 10과 연산하여 80이라는 값을 x값에 저장하고 트랜잭션 1번은 종료하였다.

하지만, 트랜잭션 2번이 종료되기전 어떠한 이유로 인해 중단되어 롤백이 된다면 y는 기존 값이었던 20으로 되돌아 가버리게 된다.

즉, 트랜잭션1에서 실행되었던 내용은 트랜잭션2가 롤백되면서 유효하지 않은 값으로 실행이 된 이상한 현상을 Dirty read라 한다.

### 2. Non-repeatable read

- 같은 데이터의 값이 달라짐

<img width="700" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/bfde6315-0f77-48dc-8dd3-6699340e993d">

- 트랜잭션1
    - x를 두 번 읽는다
- 트랜잭션2
    - x에 40을 더한다.

트랜잭션1이 먼저 시작했을때, x를 처음 읽었을때 10이라는 값을 읽고 2번째 읽기전에 트랜잭션2가 x를 읽고 40이라는 값을 더하여 저장하였다.

이후, 트랜잭션1이 x의 값을 읽었더니 50이었고 처음 읽었던 값과 달라진 것이다.

즉, 하나의 트랜잭션에서 같은 값을 두 번 읽었는데 데이터의 값이 다르게 나오는 현상을 Non-repeatable read라고 한다.

### 3. Phantom read

- 없던 데이터가 생김

<img width="700" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/0953f81f-02b7-4dcb-8d41-afeb32e60f22">

- 트랜잭션1
    - v가 10인 데이터를 두 번 읽는다.
- 트랜잭션2
    - t2의 v를 10으로 바꾼다.

트랜잭션1이 먼저 시작했을때, v가 10인 튜플을 먼저 읽어온다. 이때 트랜잭션2가 t2의 v를 10으로 바꾸고 커밋한다.

이후, 트랜잭션1이 v가 10인 튜플을 읽었더니 처음 읽었던 결과 값가 다르게 나왔다.

즉, 하나의 트랜잭션에서 같은 값을 두 번 읽었는데 없던 데이터가 생긴 현상을 Phantom read라 한다.

## 2. Isolation level

앞서 살펴본 이상현상들을 다 지켜진다면 여러가지 제약사항들로 인해 성능이 떨어지게 된다.

그래서, 일부 이상한 현상은 허용하는 몇 가지 level을 만들어서 사용자가 필요에 따라서 적절하게 선택할 수 있도록 한 Isolation level에 대해 살펴보자.

| Isolation level | Dirty read | Non-repaetable read | Phantom read |
| --- | --- | --- | --- |
| Read uncommitted | O | O | O |
| Read committed | X | O | O |
| Repeatable read | X | X | O |
| Serializable | X | X | X |

각각의 Isolation level 별로 밑으로 갈수록 허용하는 범위가 좁아진다. Serializable 같은 경우는 위 3개를 제외하고도 아예 이상 현상 자체가 발생하지 않는 Level을 의미한다.

### 1. READ UNCOMMITTED

READ UNCOMMITTED 격리 수준에서는 위 그림처럼 각 트랜잭션에서의 변경 내용이 COMMIT이나 ROLLBACK 여부에 상관 없이 다른 트랜잭션에서 보여지게 된다.

![image](https://github.com/Jammini/TIL/assets/59176149/c6528756-b01f-467d-8916-422bfac9ba50)

사용자 A가 삽입한 내용이 커밋되지 않았는데도 사용자 B가 조회가 된다. 사용자 A가 트랜잭션 작업 도중 문제가 발생하여 롤백되었다면 사용자 B는 잘못읽은 데이터를 조회가 된 것으로 여겨질 것이다.

이처럼, 더티 리드가 허용되는 격리 수준은 READ UNCOMMITTED이다.

### 2. READ COMMITTED

이 레벨에서는 위 READ UNCOMMITTED 수준에서 발생할 수 있는 더티 리드와 같은 현상은 발생하지 않는다. 어떠한 트랜잭션에서 데이터를 변경하더라도 커밋이 완료된 데이터만 다른 트랜잭션에서 조회할 수 있기 때문이다.

![image](https://github.com/Jammini/TIL/assets/59176149/7dad79e5-4cbf-4d51-be64-4b23b5e7d075)

사용자 A는 emp_no = 50000인 사원의 first_name을 "JuBal"에서 "Toto"로 수정했는데, 이 때 새로운 값인 "Toto"는 employees 테이블에 즉시 기록되고 이전 값인 "JuBal"은 Undo 영역으로 백업된다. 만약 사용자 A가 이러한 변경 내역을 커밋하기전에 사용자 B가 emp_no = 50000인 사원을 조회하면 결과값은 "Toto"가 아닌 이전 값인 "JuBal"이 조회가 된다. 여기서 사용자 B의 SELECT 쿼리 결과는 employees 테이블이 아닌 UNDO 영역의 백업된 레코드에서 가져온 결과이다.

이처럼, READ COMMITTED 격리 수준에서는 어떤 트랜잭션에서 변경한 내용이 커밋되기 전까지는 다른 트랜잭션에서 그러한 변경 내역을 조회할 수 없기 때문이다.

### 2-1. NON-REPEATABLE READ 발생

![image](https://github.com/Jammini/TIL/assets/59176149/f3e248d1-0e94-4b20-beee-f80981120517)


위 그림에서 사용자 B가 BEGIN 명령으로 트랜잭션을 시작하고 first_name = 'Toto'인 사원을 조회하면 일치하는 데이터가 존재하지 않는다.

하지만, 이후에 사용자 A가 emp_no = 50000인 사원의 이름을 'Toto'로 수정하고 커밋한 후 사용자 B는 동일한 쿼리로 조회하면 이번에는 결과가 1건이 조회가 된다.

이는 별다른 문제는 없어보이나 사용자 B가 하나의 트랜잭션내에서 동일한 SELECT 쿼리를 실행했을 때 항상 같은 결과를 보장해야 한다는 "REPEATABLE READ" 정합성에 어긋나게 된다.

### 3. REPEATABLE READ

REPEATABLE READ는 MySQL의 InnoDB 스토리지 엔진에서 기본적으로 사용되는 격리 수준이다. 이 격리 수준에서는 READ COMMITTED 격리 수준에서 발생하는 NON-REPEATABLE READ 부정합이 발생하지 않지만, PHANTOM READ 부정합이 발생한다.

![image](https://github.com/Jammini/TIL/assets/59176149/2c43406c-2e56-4f6c-8aeb-02dcae9d99c4)

REPEATABLE READ는 언두(Undo) 영역에 백업된 이전 데이터를 통해 트랜잭션 내에서는 동일한 결과를 보여 주도록 보장하여 NON-REPEATABLE READ 문제를 해결한다.

READ COMMITTED 격리 수준 또한 언두 영역에 백업된 이전 데이터를 보여 주지만, 두 격리 수준에는 언두 영역을 활용하는 방식이 다르다. REPEATABLE READ 격리 수준은 ‘언두 영역에 백업된 레코드의 여러 버전 가운데 몇 번째 버전을 보여 주냐’에 차이가 있어 NON-REPEATABLE READ 문제를 해결할 수 있다.

쉽게 말하자면, 언두 영역에 백업된 모든 데이터에는 변경을 발생한 트랜잭션의 번호가 포함되어 있는데, REPEATABLE READ 격리 수준에서는 실행 중인 트랜잭션보다 작은 트랜잭션에서 변경한 데이터만 보게 하여 NON-REPEATABLE READ 문제를 해결한다.

### 3-1. PHANTOM READ 발생

사용자 A가 employees 테이블에 INSERT를 실행하는 도중에 사용자 B가 SELECT .. FOR UPDATE 쿼리로 employees 테이블을 조회했을 때의 결과이다.

![image](https://github.com/Jammini/TIL/assets/59176149/c5945dbc-6661-4dfd-acaf-28130af0a014)

사용자 B가 트랜잭션 시작 이후 SELECT 쿼리를 수행함에 따라 동일한 트랜잭션 내에서는 결과가 동일해야 한다.

하지만, 사용자 B가 실행하는 두 번으 SELECT 쿼리 결과는 서로 다르게 되며 수행한 변경 작업에 의해 레코드가 보였다 안보였다가 하는 현상을 PHANTOM READ라 한다.

SELECT .. FOR UPDATE 쿼리는 SELECT하는 레코드에 쓰기 잠금을 걸어야 하는데, 언두 레코드에는 잠금을 걸 수 없다.

따라서, 위와 같은 쿼리는 언두 영역의 변경 전 데이터를 가져오는 것이 아니라 현재 레코드의 값을 가져오게 되버린다.

### 4. SERIALIZABLE

가장 단순한 격리 수준이자 가장 엄격한 격리 수준이다. 동시 처리 성능도 다른 트랜잭션 격리 수준보다 현저히 떨어지게 된다.

트랜잭션의 격리 수준이 SERIALIZABLE로 설정되면 읽기 작업도 공유 잠금(읽기 잠금)을 획득해야 하며, 동시에 다른 트랜잭션은 그러한 레코드를 변경할 수 없다.

즉, 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다.

## 3. 결론

**트랜잭션 동시 실행시 이상현상** 

- **DIRTY READ**: 트랜잭션내에서 커밋하지 않은 데이터에 다른 트랜잭션의 접근가능 현상
- **NON-REPEATABLE READ**: 동일한 SELECT 쿼리를 실행했을 때 항상 같은 결과를 보장해야 하지만 값이 달라지는 현상
- **PHANTOM READ**: 한 트랜잭션내에서 동일한 쿼리를 두 번 수행했는데, 첫 번째 쿼리에서 존재하지 않던 유령(Phantom) 레코드가 두 번째 쿼리에서 나타나는 현상

**트랜잭션 격리 수준**

- **READ** **UNCOMMITTED**: 트랜잭션내에서 커밋하지 않은 데이터에 다른 트랜잭션의 접근이 가능
- **READ** **COMMITTED**: 트랜잭션내에서 커밋된 데이터만 다른 트랜잭션이 읽는 것을 허용
- **REPEATABLE** **READ**: 트랜잭션 내에서 한 번 조회한 데이터를 반복해서 조회해도 결과는 동일
- **SERIALIZABLE**: 가장 엄격한 격리 수준으로 완벽한 읽기 일관성 모드 제공

### 참고

- https://www.youtube.com/watch?v=bLLarZTrebU
- https://steady-coding.tistory.com/562
