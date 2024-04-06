# 3장 시스템 설계 면접 공략법

### 시스템 설계 면접이 있는 이유

설계 기술을 시연하는 자리이고, 설계과정에서 내린 결정들에 대한 방어 능력을 보이는 자리이며, 면접관의 피드백을 건설적인 방식으로 처리할 자질이 있음을 보이는 자리이다.

## 효과적 면접을 위한 4단계 접근법

### 1단계 문제 이해 및 설계 범위 확정

엔지니어가 가져야 할 가장 중요한 기술은 올바른 질문을 하는 것, 적절한 가정을 하는 것, 시스템 구축에 필요한 정보를 모으는 것이다.

요구 사항을 정확히 이해하는데 필요한 질문들을 생각해 볼 수 있다.

- 구체적으로 어떤 기능들을 만들어야 하나?
- 제품 사용자 수는 얼마나 되나?
- 회사의 규모는 얼마나 빨리 커지리라 예상하나? 석 달, 여섯 달, 일년 뒤의 규모는 얼마가 되리라 예상하는가?
- 회사가 주로 사용하는 기술 스택은 무엇인가? 설계를 단순화하기 위해 활용할 수 있는 기존 서비스로는 어떤 것들이 있는가?

요구사항을 이해하고 모호함을 없애는게 이 단계에서 가장 중요하다는 것을 명심하자.

### 2단계 개략적인 설계안 제시 및 동의 구하기

개략적인 설계안을 제시하고 면접관의 동의를 얻는 단계이다.

- 설계안에 대한 최초 청사진을 제시하고 의견을 구하라.
- 화이트보드나 종이에 핵심 컴포넌트를 포함하는 다이어그램을 그려라. 클라이언트(모바일/웹), API, 웹 서버, 데이터 저장소, 캐시, CDN, 메시지 큐 같은 것들이 포함될 수 있을 것이다.
- 최초 설계안이 시스템 규모에 관계된 제약사항들을 만족하는지를 개략적으로 계산해보라.

### 3단계 상세 설계

이 단계까지 왔다면 다음 목표는 달성한 상태일 것이다.

- 시스템에서 전반적으로 달성해야 할 목표와 기능 범위 확인
- 전체 설계의 개략적 청사진 마련
- 해당 청사진에 대한 면접관의 의견 청취
- 상세 설계에서 집중행야 할 영역들 확인

**이제 해야할 일**

- 설계 대상 컴포넌트 사이의 우선순위를 정하기
- 면접관이 집중하고 있는 영역을 보자
    - ex. 단축 URL 생성기라면, 해시 함수의 설계를 구체적으로 설명해야한다.
    - ex. 채팅 시스템라면, 레이턴시를 어떻게 줄이고, 사용자의 온/오프라인 상태를 표시할 것인가?
- 불필요한 세부사항에 시간을 쓰지말고, 내가 입증해야하는 능력에 써야하는 시간에 쓰자.

### 4단계 마무리

- 개선할 점은 언제나 있기 마련이다.
- 만든 설계를 한번 다시 요약해주는 것도 도움이 될 수 있다.
- 운영 이슈도 논의할 가치가 충분하다. 메트릭은 어떻게 수집하고 모니터링 할것인가? 로그는? 배포는?
- 미래에 닥칠 규모 확장 요구에 어떻게 대처할 것인지도 흥미로운 주제다.

### 해야 할 것

- 질문을 통해 확인하자. 스스로 내린 가정이 옳다 믿고 진행하지 마라
- 문제의 요구사항을 이해하라
- 정답이나 최선의 답안 같은 것은 없다. 스타트업, 대기업의 설계는 당연히 다르다. 요구사항을 정확하게 이해했는지 확인하라
- 나의 사고 흐름을 이해할 수 있도록 면접관과 소통하라
- 가능하다면 여러 해법을 함께 제시하라
- 개략적 설계에 면접관이 동의하면, 세부사항을 설명하기 시작하라. 가장 중요한 컴포넌트부터!
- 면접관의 아이디어를 이끌어내라
- 포기하지 말라

### 하지 말아야 할 것

- 전형적인 면접 문제들에도 대비하지 않은 상태에서 면접장에 가지 말라
- 요구사항이나 가정들을 분명히 하지 않은 상태에서 설계를 제시하지 말라
- 처음부터 세부사항까지 깊이 설명하지 말라. 개략적 설계부터!
- 진행 중에 막혔다면 힌트 주기를 주저하지 말라
- 소통을 주저하지 말라. 침묵 속에 설계를 진행하지 말라
- 설계안을 내놓는 순가 면접이 끝난다고 생각하지 말라. 면접관이 끝났다고 말하기 전까지는 끝난 것이 아니다. 의견을 일찍, 그리고 자주 구하라

### 시간 배분

1. 문제 이해 및 설계 범위 확정: 3분~10분

2. 개략적 설계안 제시 및 동의 구하기: 10분~15분

3. 상세 설계: 10분~25분

4. 마무리: 3분~5분