SK AI SUMMIT 2024 에서 발표한 LLM orchestration 기술을 활용한 고객센터 에이전트 개발 사례 리뷰이다.[링크](https://www.youtube.com/watch?v=xTcDRbYuEas&t=467s)

ChatGPT의 등장으로 대화형 Agent를 연구하는 연구자들의 흐름이 많이 바뀌었다. 하지만 점점 현실을 깨닫기 시작하는데 할루시네이션 문제, 학습한 지식의 제한, 추론 능령 부족, 모델의 속도 및 안정성 등 다양한 문제에 직면한다.
그래서 이러한 약점 극복을 위해 아래와 같이 발전해 왔다.
1. LLM Foundation Model의 성능 향상
2. 외부 Tool을 활용하는 방안(LLM agent)
![](assets/Pasted%20image%2020250113202526.png)
위 방식 중 LLM의 성능은 지금 기술력으론 꽤 많이 올라온 상태이다. GPT를 필두로 오픈소스의 LLAMA등 좋은 모델이 많이 등장하였다. 그래서 현 시점 LLM의 약점을 극복하기 위해 가장 핫한 방법은 외부 Tool을 활용하는 방법이다.
초반에는 **PlugIn 혹은 Function이라는 말을 많이 사용했는데 최근에는 이제 Tool이라는 말을 사용한다.**
결국 LLM이 해결하기 어려워 하는 방식에 대하여 외부 Tool이 지원을 해주는 방식인데 특히 **실시간 정보 검색이나, 최신 데이터를 제공하는 문제, 복잡한 수학 문제, 전문 지식이 필요한 답변등은 외부 Tool이 해결을 해주는 방식을 사용한다.**
나머지 하나는 이 발표의 메인 주제인 LLM의 orchestration 기술이다.
![](assets/Pasted%20image%2020250113203211.png)
>출처: https://github.com/a16z-infra/llm-app-stack

LLM Orchestration이란 **LLM을 효과적으로 활용하기 위해서 여러 작업을 자동화하고 관리하는 기술이다.** 단순히 모델을 호출하는 것을 넘어, Prompt Chaing, 외부 API 연동, 데이터 처리, 상태 관리 등의 여러 기능을 통합해 효율적인 LLM 어플리케이션을  구성하는 데 중점을 둔다.
새로운 개념에 의한 단어가 아닌 우리가 LLM 서비스를 구현하기 위해서 워크플로우를 짜는 것을 Orchestration이라고 하며 Orchestration의 목표는 빈도가 높고 반복할 수 있는 프로세스의 실행을 간소화 및 최적화하여 데이터 팀이 복잡한 작업과 워크플로우를 간편하게 관리하도록 돕는 것 이다.
대표적인 프레임 워크로는 아래가 있다.
- Langchain
- LlamaIndex
Orchestratin의 기술들은 아래와 같다.
![](assets/Pasted%20image%2020250113203945.png)
![](assets/Pasted%20image%2020250109202620.png)
>출처: https://www.youtube.com/watch?v=xTcDRbYuEas

LLM의 성능을 높이기 위해서 두 번째 사진과 같이 POC를 진행을 하면서 발전해 왔다. (본 프로젝트는 출력 시간 보단 정확도에 초점을 둔 프로젝트인 거 같다.)
고객들이 LLM에 기대하는 수준은 매우 높다. 그렇기에 최대한 LLM의 강점을 극대화 하고, 부족한 부분은 보완하는 전략이 필요하다.

LLM의 강점
- LLM이 처리 가능한 난이도의 질문은 일당백이다.
- 맥락 및 언어의 이해
- Reasoning
- 자연스러운 글쓰기 및 콘텐츠 생성
- 질문 응답 및 요약
- 코드 작성
LLM의 약점
- 풀 수 없는 문제를 그럴듯하게 답변을 생성해서 거짓말함(Hallucination)
- 최신 정보 혹은 학습하지 않은 정보에 대한 답변 불가
- Action의 정확도 100% 보장 못함
- 사실성 검증 부족
![](assets/Pasted%20image%2020250113205749.png)
할루시네이션 문제는 Orchestration으로 처리가능한 대표적인 방법이다. 문제를 조절한다는 말이 어려울 수 있는데 문제가 어렵다는 가정하에 Prompt에 더욱 많은 정보를 넣거나 혹은 LLM을 한번만 돌리지 말고 문제의 기점을 두 번으로 나눠서 LLM에게 주문해 조금 더 답변 생성을 쉽게 만들거나 혹은 외부 Tool을 이용을 해서 그 정보를 LLM input에 추가시켜 답변하게 만들거나 여러가지 방법이 존재한다.
위와 같이 LLM의 약점을 극복하기 위해서는 Tool을 활용하면 최대한 극복 가능하게 만들어 준다.
하지만 이렇게 Tool을 사용하고 Prompt를 길게 적고 하다 보면 초기에는 목표 값에 빠르게 다가가지만 어느순간 정체되는 구간이 오는 데 이때 Prompt의 양을 늘리다 보면 결국 다시 결과가 안 좋아지는 상황이 온다. Prompt의 양이 늘어나게 되면 Token의 수가 기하급수적으로 늘어나 비용이 많이 늘어 나기에 조절을 해야한다.
![](assets/Pasted%20image%2020250113211032.png)
그래서 위와 같이 실제 프로젝트할 때 도움이 되었던 Prompt Eng 방법들에 대해서 설명을 해준다. 또한 prompt Caching도 많이 중요하다고 이야기 하는데 자주 반복되는 쿼리에 대해서는 미리 Prompting과 응답을 저장해 재사용하는 방식으로 진행을 하면 아래와 같이 많은 비용을 절약할 수 있다.
![](assets/Pasted%20image%2020250113211323.png)
강연자는 지금 상황에서 만족하지 않고 추후 발전할 방향도 제시를 했는데 Single Agent 모델에서 Multy-Agent로 업데이트를 시킬려고 한다.
![](assets/Pasted%20image%2020250113211459.png)
결국 Agent를 하나만 사용하지 않고 앙상블 모델 마냥 다수의 Agent를 사용하여 LLM의 정확도를 더욱 높이겠다.는 내용이다.
### Reference
https://www.youtube.com/watch?v=xTcDRbYuEas&t=467s
https://velog.io/@mertyn88/LLM-Rag-Architecture