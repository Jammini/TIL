# 댓글 알고리즘으로 무한 depth 구현하기

### 목차

1. [개요](#1-개요)
2. [최대 2depth 댓글](#2-최대-2depth-댓글)

   2-1. [댓글 생성 삭제](#2-1-댓글-생성-삭제)

   2-2. [댓글 목록 조회](#2-2-댓글-목록-조회)

3. [무한 depth 댓글](#3-무한-depth-댓글)

   3-2. [댓글 생성 삭제](#3-2-댓글-생성-삭제)

4. [결론](#4-결론)

## 1. 개요

처음에는 최대 2depth의 대댓글을 구현하고 이후 추가적으로 무한 depth의 대댓글을 구현해보았다.

## 2. 최대 2depth 댓글

### 2-1. 댓글 생성 삭제

기본적인 요구사항은 다음과 같다.

- 계층별로 오래된순으로 정렬을 한다.
- 하위 댓글이 모두 삭제되어야 상위 댓글을 삭제할 수 있다.
    - 하위 댓글이 X, 댓글 즉시 삭제
    - 하위 댓글이 O, 댓글 삭제 표시

![Image](https://github.com/user-attachments/assets/03b17763-73de-4bed-bb8a-2339562e3ce6)

여기서 댓글 2를 삭제하게 돠면 아래와 같다.

![Image](https://github.com/user-attachments/assets/1c1d8eea-923f-4e70-b7dc-c1a17d9b8cc0)

댓글2 하위 계층에 댓글5가 존재하기 때문에 댓글 2는 삭제 표시만 된다.

사용자에게는 “삭제된 댓글입니다” 표시해준다.

![Image](https://github.com/user-attachments/assets/29e1d504-2ab4-4ed2-a200-b5f684829411)

댓글1과 댓글 5를 삭제하면 어떻게 될까?

![Image](https://github.com/user-attachments/assets/a9aba859-2ff0-4095-9fa4-4daea67ce0dc)

댓글1은 삭제표시, 댓글5는 즉시 삭제가 처리된 것을 확인할 수 있다.

![Image](https://github.com/user-attachments/assets/cdf617e0-2016-420e-afe5-c11e714b2658)

추가적으로 댓글2는 아래 하위댓글이 없기 때문에 재귀적으로 삭제된다.

댓글1은 하위 댓글3이 남아 있기 때문에 삭제표시 상태를 유지한다.

### 2-2. 댓글 목록 조회

| id | PK |
| --- | --- |
| content | 내용 |
| post_id | 게시글 ID |
| parent_comment_id | 상위 댓글 ID |
| nickname | 작성자 닉네임 |
| deleted | 삭제여부 |
| created_at | 등록일시 |

계층 관계를 알아야 하므로 상위 댓글의 참조 키가 필요하다.

각 댓글은 상위 댓글 ID(parent_comment_id) 를 가진다.

댓글을 정렬은 다음과 같다.

![Image](https://github.com/user-attachments/assets/5e389e5d-01db-4079-855e-32a0434318a0)

단순히 id나 생성시간으로는 정렬 순서가 명확하지 않다.

댓글3이 댓글 4보다 먼저 작성되었지만, 댓글 1의 대댓글인 댓글4가 먼저 노출된다.

상위 댓글은 하위 댓글보다 반드시 먼저 생성된다.

상위댓글을 가진 하위 댓글은 생성된 시간 순으로 정렬이 된다.

즉, 상위 댓글은 항상 하위 댓글보다 먼저 생성되고, 하위 댓글은 상위 댓글 별 순서대로 생성된다.

상위 댓글에 의해 순서가 명확하게 정해질 수 있는 것이다.

![Image](https://github.com/user-attachments/assets/4fc36593-4b11-43cd-bc09-25976df2d309)

루트 댓글의 parent_comment_id는 본인의 id를 넣어준다.

정렬을 보면 parent_comment_id를 기준으로 오름차순, id 오름차순의 정렬 구조를 가진다.

## 3. 무한 depth 댓글

depth가 늘어남에 따라 최대 2depth와 동일한 방식으로 정렬 순서를 나타내기에는 어렵다.

![Image](https://github.com/user-attachments/assets/8703eeb0-0a9c-4285-a39c-4fc58c91d639)

무한 depth에서는 상하위 댓글이 무한으로 생성 될 수 있어 정렬순을 나타내기 위해 모든 상위 댓글의 정보가 필요할 수 있다.

![Image](https://github.com/user-attachments/assets/22fa38d8-8e16-42a9-90b0-421cd501382b)

1depth부터 각 댓글의 depth의 모든 경로를 가지고 있어 모든 경로 정보를 표현한다.

parent_comment_id 에서 가진 경로정보를 이용해 문자열 컬럼 1개로 경로 정보를 만들려고 한다.

각 depth에서의 순서를 문자열로 나타내고, 이러한 문자열을 순서대로 결합하여 경로를 나타내는 것이다.

이러한 방식을 **Path Enumeration(경로 열거**) 방식이라 한다.

![Image](https://github.com/user-attachments/assets/4ceda8bb-f8e5-453a-97d7-4615e6ec99c3)

위와 같이 각 depth 별로 5개의 문자열로 경로 정보를 저장한다.

1depth는 5개의 문자로 2depth는 10개의 문자로….. Ndepth는 (N * 5)개의 문자로…

각 경로는 상위 경로를 상속하면서 독립적이고 순차적인 경로를 가진다.

![Image](https://github.com/user-attachments/assets/5a14a58f-e0c5-41d8-8198-fae47ec8c768)

좌측 그림과 같은 계층형 댓글은 우측 그림과 같은 경로 정보를 가질 것이다.

각 경로는 상위 댓글의 경로를 상속하며, 각 댓글마다 독립적이고 순차적인(문자열 순서) 경로가 생성된다.

10개의 숫자로 나타내면 표현할 수 있는 경로 범위가 10^5 (‘00000’ ~ ‘99999’) 개로 제한된다.

문자열이기 때문에 숫자로만 나타낼 필요 없이 0~9, A-Z, a-z등 문자로 사용할 수도 있다.

즉, 표현할 수 있는 범위는 숫자와 문자를 다 사용하게 되면 (0~9) 10개,  (A~Z) 26개 (a-z) 26개)

62^5 = 916,132,832개로 제한된다.

### 3-1. 댓글 생성 삭제

depth가 늘어남에 따라 생성하는 로직이 바뀌었지만 삭제는 2depth와 같은 전략을 사용한다.

아래 그림과 같이 댓글 목록이 있을때 신규 댓글 작성 요청이 왔다.

![Image](https://github.com/user-attachments/assets/60e11dfd-8f10-4f0d-a316-d6f8f3fbd243)

00000의 하위 댓글 중에서 가장 큰 path(childrenTopPath) 00000 00001을 찾고 여기에 1을 더한 문자열이 신규 path가 된다.

![Image](https://github.com/user-attachments/assets/b3bc08b1-9a01-465b-a932-e9a190d00a39)

생성을 하기 위해서 childrenTopPath를 빠르게 찾는게 중요한데 어떻게 찾을 수 있을까?

각 댓글의 모든 자식 댓글은 prefix가 상위 댓글의 path로 시작한다.

즉, 00000의 모든 자식들은 prefix가 00000으로 시작한다.

그 중 00000이라는 prefix를 가지는 모든 자식 댓글 중에서 가장 큰 path는 descendantsTopPath가 되고 descendantsTopPath는 신규 댓글의 depth와 다르지만 childrenTopPath를 포함한다.

descendantsTopPath에서 (신규 댓글의 depth * 5)까지만 남기고 잘라낸다.

00000 00001 00000 에서 (신규 댓글의 depth 2) * 5 = 10개의 문자만 남기고 잘라낸다.

그러면 childrenTopPath = 00000 00001 을 찾을 수 있다.

![Image](https://github.com/user-attachments/assets/97618c8a-b97b-4100-a25a-f3ac7ddfd30b)

이제 childrenTopPath에서 여기에 1을 더해주면 신규 댓글의 path가 만들어진다.

상위 댓글의 parentPath = 00000 을 상속하고 00001 다음은 00002 이므로 신규댓글의 path 는 00000 00002가 된다.

### 3-2. 댓글 목록 조회

| id | PK |
| --- | --- |
| content | 내용 |
| post_id | 게시글 ID |
| path | 계층 경로 |
| nickname | 작성자 닉네임 |
| deleted | 삭제여부 |
| created_at | 등록일시 |

2depth 댓글에서 상위 댓글의 참조 키로 사용했던 parent_comment_id 대신 계층 관계를 알아야 하므로 path를 이용해 문자열 계층 경로를 나타내주었다.

각각의 댓글은 path에 문자열을 순서대로 결합하여 경로를 나타내다.

댓글 목록은 다음과 같이 정렬된다.

![Image](https://github.com/user-attachments/assets/bda7742b-992c-4583-9d2b-2a350a1b32b6)

path를 이용해서 정렬을 해주고 아래와 같은 구조로 댓글 목록을 나타낸다.

![Image](https://github.com/user-attachments/assets/921dc7f3-7e37-4eb3-83ac-6e7c774d1c23)

## 4. 결론

댓글 알고리즘은 위에서 구현한 것과 별개로 Adjacency List, Nested Set, Path Enumeration 등 여러가지가 존재한다.

그 중 저는 경로 열거(path enumeration) 방식을 선택하여 무한 depth를 구현하였다.

이 방식은 경로가 길어지면 문자열 연산 비용이 증가 되고 경로 수정이 필요한 경우 업데이트 부담이 증가한다는 단점을 가지고 있다.

하지만, 직관적으로 댓글에 대해 경로를 알 수 있고 전체 계층을 한 번에 가져올 수 있고 부모-자식 관계를 유지하면서 빠르게 검색이 가능하기 때문에 이 방식을 이용해 댓글을 구현하였다.
