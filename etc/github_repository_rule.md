# Github 저장소에 Repository Rule 제약하기

### 목차

1. 개요
2. 저장소 규칙(Repository Rules)
3. 결론

### 1. 개요

23년 7월에 Github 저장소에 적용할 수 있는 [저장소 규칙 기능(Repository Rule)](https://github.blog/news-insights/product-news/github-repository-rules-are-now-generally-available/)을 정식으로 공개했다.

Repository를 혼자만 사용하면 규칙을 스스로 정해서 사용하면 되겠지만, 여러 사람과 협업하기 위해선 저장소 룰을 정해서 사용하면 관리하기 용이해진다.

나는 간단히 저장소에 특정 인원 수 이상에게 Approve를 받아야 Merge가 가능하도록 규칙을 만들려고 한다.

차근차근 하나씩 시작해보자.

### 2. **저장소 규칙(Repository Rules)**

<img width="699" alt="image" src="https://github.com/user-attachments/assets/7c5f82f9-892d-42fa-959d-24d997cceea7">

Repository의 Settings 메뉴에서 하위에 Rulesets라는 메뉴를 볼 수 있다.

New ruleset을 이용해 새로운 branch ruleset을 등록해준다.

![image](https://github.com/user-attachments/assets/8c6f8288-014b-44ff-921a-281532e4cc0d)

- Ruleset Name: 규칙 이름 지어주기(필수)
- Enforcement status: Disabled 되어있는데 Active로 바꿔줘야 적용 된다.
- Bypass list: 이 규칙에 얽매이지 않아도 되는 사람 설정해주기
- Target branches: 여기에 Add target 눌러서 Include default branch 해주면 default branch인 main이 보호된다. == Pull Request를 해서 merge 한 게 들어갈 브랜치가 보호된다.
- Branch protections: Restrict deletions, Block force pushes(강제 푸시 금지) 가 기본으로 활성화되어있고, 우리가 선택해줘야하는 건 Require a pull request before merging.Required approvals를 조정해 몇명에게 Approve를 받아야하는지 설정하면 된다.

<img width="700" alt="image" src="https://github.com/user-attachments/assets/fdb3001c-37e6-46d2-8c83-f2943e557509">

내 프로젝는 위와 같이 병합하기 전에 최소한 2명의 승인을 받아야 하고, 새로운 커밋이 푸시되면 이전에 PR 올렸던 승인은 무시되고 다시 승인을 받도록 설정하였다.

### 3. 결론

자 이제 규칙이 잘 적용되었는지 확인해보자.

<img width="699" alt="image" src="https://github.com/user-attachments/assets/fc568ab6-a24e-49cd-83f6-e93c43782a1f">

<img width="702" alt="image" src="https://github.com/user-attachments/assets/a5ac9b78-4ef2-4d01-842d-81e63754ef05">

At least 2 approving reviews are required by reviewers with write access.

위와 같이 현재 approval이 1명 있지만 최소 2명의 approving review가 리뷰어에게 필요하다고 안내해주고 있으며 Merge pull request 버튼이 활성화 되지 않은 상태인 것을 확인할 수 있다.

<img width="702" alt="image" src="https://github.com/user-attachments/assets/adb63c7c-3539-4c4f-9863-920345c11e63">

이처럼, 최소 2명에게 받아야만 Merge pull request가 활성화 되어 있는 것을 볼 수 있다.

추가적으로, 저장소 규칙을 더 알아보고 싶다면 Github에서 제공한 [저장소규칙](https://github.blog/news-insights/product-news/github-repository-rules-are-now-generally-available/)을 확인해보자.