#LLM #페르소나 #개인화 #Prompt 
## 0. Abstract
- 페르소나(persona) 개념은 최근 LLM 대규모 언어 모델을 특정 맥락에 맞게 조정하기 위한 유망한 프레임워크 이다.
- 본 논문은 LLM 페르소나의 연구가 증가하지만 체계성이 부족하고 분류 체계가 결여되어 있는 본 주제에 대해 조사하여 Survey를 진행 했다.
- 대표적인 방향은 아래와 같다.
	1. LLM 역할 수행(Role-Playing) - LLM에 특정 페르소나를 부여하는 방식
	2. LLM 개인화(Personalization) - LLM이 개인 페르소나를 반영하여 처리하는 방식(그냥 개인의 특징을 반영한다고 나는 생각한다.)
- **기본적으로 서베이 논문이기에 기술적 설명보단 논문에 관한 정보가 많다. 그래서 최대한 링크를 걸겠다.**

## 1. Introduce
- 대규모 언어 모델은 많은 발전을 이룸
- 범용 챗봇 이외에도 LLM을 즉정 맥락에 맞게 어떻게 적용시킬 것인가 하는 문제가 관건이다.
- 이를 통해 페르소나를 활용하여 특정 시나리오에서 LLM 을 적응시키는 방법으로 관점이 다시 부상하였다.
![](assets/Pasted%20image%2020250831172653.png)
- 본 논문에서 그림1과 같이 현재의 연구를 LLM 역할 수행(Role-Playing)과 LLM 개인(Personalization) 라는 두 가지 주요 흐름으로 구분하여 설명한다. 이는 그림 1에 설명이 있다.
	- LLM Role-Playing: LLM은 부여된 페르소나를 수행하도록 과제를 부여받으며, 환경적 피드백을 바탕으로 행동하고 환경에 적응한다.(페르소나가 LLM에게 속함)
	- LLM Personalization: LLM은 사용자 페르소나(예: 배경 정보, 과거 행동, 선호도)를 반영하여 개별화된 요구를 충족시키고, 특정 사용자에 적응하도록 과제를 부여받는다.(페르소나가 사용자에게 속함)
![](assets/Pasted%20image%2020250831172802.png)
## 2. LLM Role-Playing
- LLM 기반 언어 에이전트는 최근 Planning. reflection, tool-use와 같은 능력이 발견됨
- 여기서 말하는 페르소나는 되게 광범위하게 정의 하는거 같다.
	- `LLM role-playing is by coupling personas with language agents, specifically, by incorporating personas directly inside the prompt of language agents.`
		- 위 구문을 보면 Role-Playing의 주요 접근법은 페르소나를 언어 에이전트와 연결하는 걸로 아래 논문을 예를 든다.
		- 대표적인 Prompt Eng의 방식들이다. 이러한 방식은 언어 Agent에 페르소나를 직접적으로 넣는다고 말하고 있다.
	> [React](obsidian://open?vault=PARA&file=Area_of_responsibility%2FML%26AI%2F%EB%85%BC%EB%AC%B8_React%2F%EB%A6%AC%EB%B7%B0_React)
		- ReAct는 언어 모델이 **추론(thought)과 행동(action)을 번갈아 수행**하도록 하여, 단순한 체인 오브 소트 방식의 한계를 극복하고 **정확한 추론과 효율적인 환경 상호작용을 동시에 달성**하는 프레임워크다.
	> Reflexion: Language Agents with Verbal Reinforcement Learning
		- LLM 기반 Agent가 자기 피드백(Self-reflection)을 **언어적 메모리(Verbal Memory)** 로 저장해 두고 이후 의사결정(Planning, Reasoning, Code Generation 등)에 재활용하도록 하는 프레임워크다.
		- 모델 파라미터를 바꾸지 않고도 **자기 반성적 언어 피드백(Verbal Reinforcement)** 을 통해 성능을 크게 높일 수 있다는 점을 보여준다.
	> Tree of thoughts: Deliberate problem solving with large language models
		- **ToT**는 Language Model(LLM)이 **Chain of Thought(CoT)** 처럼 일직선으로 사고하지 않는다.
		- 여러 가능한 "생각(thoughts)"을 **트리 구조(Tree)** 로 탐색하며 **선택, 평가, 돌아가기(backtracking)** 등을 통해 **탐색(search) 기반의 문제 해결**을 수행하게 하는 프레임워크
		- LLM이 여러 사고 경로를 전략적으로 탐색하면서 계획(planning), 평가, 되돌림(backtracking)을 통해 더 효과적으로 문제를 해결하게 만드는 구조적 접근

- Role-Playing을 지시받은 LLM은 부여받은 페르소나로 지시된 응답에 맞는 답변을 생성하는데 이러한 에이전트를 여러개 둬서 협력/소통 하면서 복잡한 과제를 해결 가능하다.(멀티 에이전트)

>Large language model based multi-agents: A survey of progress and challenges
>	- LLM 기반의 다중 에이전트 시스템(LLM‑MA)이 단일 에이전트보다 복잡한 문제 해결 및 세계 시뮬레이션에 더 효과적이라는 점에 주목
>	- 특정 역할을 수행하는 여러 에이전트의 협업 구조, 통신 방식, 스킬 성장 메커니즘과 이를 다루는 데이터셋·벤치마크를 종합적으로 정리한 survey

- 이러한 멀티 에이전트는 prompt에 명신된 이름, 나이, 성격 특성에 따라 인간의 행동을 모방하여 사회적 시뮬레이션 환경에 참여한다.
> Generative agents: Interactive simulacra of human behavio
	- 메모리(Recency/Importance/Relevance) → Reflection → Planning으로 구성된 아키텍처로 LLM Agent에 지속적 맥락·자기성찰 을 부여.
	- 단 한 문장 힌트만으로도 **초대 확산·일정 조율** 등 **자발적 집단 행동**이 등장(발렌타인 파티 사례)
	- 통제/종단 평가와 **Ablation**으로 각 컴포넌트의 효과를 확인했지만, **검색 실패·환각·윤리 리스크**가 남아 있음.

### 2.1 Environments
#### 2.1.1 Software Development
(지금 궁금한 도메인 아니라 패스)
- 소프트웨어 개발의 목표는 일반적으로 프로그램을 설계하거나 코딩 프로젝트를 수행하는 것
- 기존 연구에서는 워터폴 모델(이나 표준운영절차와 같은 방법론을 활용하여 과제를 관리 가능한 하위 과제로 분해
-  최근 연구에서는 복잡한 코드 생성 작업을 해결하기 위해 여러 LLM 에이전트가 각각 전문적인 “전문가” 역할을 수행하며 분업과 협업을 포괄하는 최초의 자기 협업(self-collaboration) 프레임워크를 제안
- 워터폴 모델을 따르는 ChatDev(Qian et al., 2023)는 개발 과정을 설계, 코딩, 테스트, 문서화의 4단계 파이프라인으로 나누고, 각 단계를 원자적 하위 과제들의 순서로 분해하는 Chat Chain을 제안
- 위의 연구와 달리 MetaGPT(Hong et al., 2023a)는 LLM 에이전트가 자유 텍스트가 아닌 구조화된 출력을 생성하도록 요구하며, 목표 코드 생성의 성공률을 크게 향상시켰음을 보여줌
#### 2.1.2 Game
#### 2.1.3 Medical Application
#### 2.1.4 LLM-as-Evaluator
- 강력한 LLM을 평가자로 채택하는 개념은 언어 모델 정렬(Alignment)을 평가하기 위한 사실상의 표준 프레임워크가 됨
- LLM이 내린 판단은 기존의 전통적 평가 지표보다 인간의 실제 정답(ground-truth)과 더 높은 상관성을 보일 수 있다
- 대표적으로 LLM-as-a-judge가 존재하는데 여기서 LLM은 공정한 판사로서 역할을 수행한다.
	- 유용성(helpfulness), 관련성(relevance), 정확성(accuracy), 깊이(depth), 창의성(creativity)과 같은 요인들을 고려한다
> Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena
	- LLM 자체를 **Judge** 역할로 활용하여 챗봇 평가를 자동화하는 **LLM‑as‑a‑judge** 접근을 제안
	- GPT‑4와 같은 강력한 LLM Judge가 인간 평가자와 **80% 이상의 일치율(agreement)**을 보이며 높은 신뢰성을 입증**
	- **스케일 가능하고, 설명 가능한 평가 체계**, 즉 **hybrid evaluation framework**로 활용될 수 있다고 주장
	Large language models are diverse role-players for summarization evaluation. In CCF International Conference on Natural Language Processing and Chinese Computing
	- 자동 요약 평가에서 일반적인 지표들은 문법적 정확성(grammar), 응집도(coherence), 유익함(informativeness), 간결함(succinctness), 흥미로움(interest) 등의 다양한 차원을 충분히 반영하지 못함
	- DRPE라는 프레임워크를 제안
	- 정적인 역할(role‑player)로 설정 즉 다른 관점을 사용하는 Prompt를 설계함
	- 이러한 각각 다른 성격의 LLM을 여러개 지정하여 통합된 평과 결과도출
	Chateval: Towards better llm-based evaluators through multi-agent debate.
	- LLM 평가자(evaluator) 대신 단일 모델이 아닌 다중 LLM 에이전트(Multi‑Agent)가 토론(debate)을 통해 상호 검토
	- diverse personas 역할을 가진 평가자들이 인간 평가자와 더 높은 일치도로 생성 텍스트를 형가할 수 있다.
	- 즉 각각의 에이전트가 다른 페르소나를 가지게하여 다른 역할과 관점이 하나로 모여 결과 도출을 유도함
	- 위 와 같은 방법을 제시하는 ChatEval을 제시
### 2.2 Role-Playing Sechema
- LLM Role-Playing은 Single Agent와 Mylti-Agent로 나눠진다.
	- Single Agent
		- 하나의 에이전트가 다른 에이전트의 도움 없이 독립적으로 목표를 달성할 수 있는 경우
		- 환경적 정보와 피드백에 더 집중한다.
		>Voyager: An open-ended embodied agent with large language models
			- Mincraft에서 진행됨
			- GPT-4기반 LLM 에이전트로, 자동 커리큘럼(automatic curriculum), skill library, 반복적 프롬프팅(iterative prompting)으로 구성된 구조
			- 이를 통해 자기 주도 탐험, 지속적인 스킬 학습, 실행 오류 피드백 기반 개선을 수행
	- Multi-agent
		- 하나의 에이전트가 목표를 달성하기 위해 다른 데이전트의 지원을 받는 형식
		- 현실 세계와 유사하게, 환경 내 상호작용이 핵심적이다.
		>The rise and potential of large language model based agents: A survey
			- **large language model (LLM) 기반 에이전트**의 발전과 응용을 포괄적으로 정리하며, 특히 **brain, perception, action**으로 구성된 일반적 **framework**을 제시하고 조사한 논문
		large language model based multi-agents: A survey of progress and challenge
			- **LLM 기반 다중 에이전트(LLM‑MA) 시스템**의 발전 동향을 체계적으로 정리
		Metagpt: Meta programming for multi-agent collaborative framework
			- Standardized Operating Procedures (SOPs) 프롬프트 형태로 **meta-programming**한, **LLM 기반 다중 에이전트 협업 구조**
			- 역할 분담(role specialization)과 실행 가능한 피드백(executable feedback) 체계를 통해 효율적이고 응집력 있게 수행
		AgentVerse: Facilitating Multi-Agent Collaboration and Exploring Emergent Behaviors in Agents
			- **multi-agent 협업 프레임워크**로, **expert recruitment**, **collaborative decision-making**, **action execution**, **evaluation**의 순환 구조를 통해 다수의 에이전트가 단일 에이전트보다 뛰어난 **문제 해결 능력**을 발휘
		
### 2.3 Emergent Behaviors in Role-Playing
- 중 에이전트 스키마에서는 LLM 협업을 통해 인간 사회에서 나타나는 현상에 반응한다.
	- 자발적 행동(Voluntary Behavior)
		- 자발적 행동은 주로 협력적 협업 패러다임에서 발생하며, 에이전트가 동료를 적극적으로 돕거나 팀 목표 달성을 위해 도울 수 있는 부분이 있는지 묻는 경우
	- 동조적 행동(Conformity Behavior)
		- 동조적 행동은 에이전트가 팀 목표에서 벗어났을 때 발생한다. 다른 에이전트로부터 비판과 제안을 받은 후, 이탈한 에이전트는 자신의 행동이나 결정을 수정·조정하여 팀과 더 잘 협력
	- 파괴적 행동(Destructive Behavior)
		- 때때로 LLM은 원하지 않는 부정적이고 해로운 결과를 초래하는 다양한 행동을 한다. 예를 들어, 세계를 지배하려는 **악한 마음(Bad Mind)** 을 드러낼 수 있다
		- 이러한 파괴적 행동은 역할 수행의 안전성과 편향 문제에 대한 우려를 제기

## 3. LLM Personalization
![](assets/Pasted%20image%2020250901135936.png)
- 사용자의 의도에 LLM을 정렬(alignment)을 시키는 대표적인 접근법은 인간 피드백 기잔 강화학습(RLHF)으로 모델에 집단적 의식과 편향을 주입하는 과정이다.
### 3.1 개인화 추천(Personalized Recommendation)
![](assets/Pasted%20image%2020250901142744.png)
- 추천시스템은 사용자의 선호에 맞는 항목을 추천하는 것을 목표로 한다.
- 가장 기본적으로 연구자들은 LLM을 추천시스템에 적용하기 위해 다양한 Prompt 기법을 연구함
	>Personalized prompt learning for explainable recommendation.
		- 추천시스템에서 user와 item 정보를 효과적으로 pre-train된 LLM에게 통합하기 위해 **discrete as regularization**과 **continuous prompt learning**을 도입
		- **sequential tuning**과 **recommendation as regularization**이라는 두 가지 훈련 전략을 통해 **설명(explanation) 생성** 성능을 크게 향상
	PALR: Personalization Aware LLMs for Recommendation
		- 사용자 행동(클릭, 구매, 평점 등)을 기반으로 **검색 기반 후보 추천(candidate retrieval)** 후 LLama-7B를 파인튜닝
		- 지연어 형식의 Prompt로 추천 후보를 제시하고 사용자의 선호에 맞게 랭킹 형식으로 추천하는 시스템 구조
- 많은 연구들이 제로샷 설정에 집중하여 능력 활용
	>Zero-Shot Next-Item Recommendation using Large Pretrained Language Models
		- LLM을 추론 엔진으로 활용해 **fine-tuning 없이 zero-shot 방식**으로 다음 아이템 추천을 수행하는 **Zero-Shot NIR (Next‑Item Recommendation)** 프롬프트 전략을 제안
		- **candidate filtering**과 **3단계 prompting**를 사용하여 엄청난 결과를 보임
	 Large Language Models are Zero-Shot Rankers for Recommender Systems
		- LLM을 순수한 **zero-shot ranking model**으로 활용해, 사용자 이력을 조건으로 후보 아이템을 순위화하는 **조건부 랭킹(conditional ranking)** 문제로 공식화
		- 설계된 **prompt template**과 다양한 **prompting/bootstrapping 기법**을 통해 GPT-4를 비롯한 LLM이 **추천 후보 정렬에서 유망한 성능**을 보임
- 이러한 연구에도 추천시스템은 책임성과 신뢰성이 여전히 떨어짐
### 3.2 개인화 검색(Personalized Search)
- **개인화 검색 시스템**은 복잡한 쿼리와 과거 상호작용을 이해하여 사용자 선호를 추론하고, 여러 출처에서 정보를 종합해 응집력 있고 자연스러운 언어 형태로 제시가 가능하다.
- **인지 메모리 메커니즘(cognitive memory mechanism)** 을 LLM과 결합하여 개인화 검색에 활용하는 전략도 가능하다.
### 3.3 Personalized Education
### 3.4  Personalized Healthcare
### 3.5 개인화된 대화 생성(Personalized Dialogue Generation)
- 대화 생성 과제는 목표에 따라 (1) **과업 지향(Task-oriented) 대화 모델링(ToD modeling)** 과 (2) **사용자 페르소나 모델링(User persona modeling)** 으로 구분된다
- ToD 모델링과 사용자 페르소나 모델링을 논의한다.
	- ToD
		![](assets/Pasted%20image%2020250901152254.png)
		- ToD 모델링은 호텔 예약이나 음식점 예약과 같은 특정 과업을 여러 상호작용 단계를 통해 사용자에게 안내한다
		- Hudeček and Dusek (2023)은 **지시 기반 학습(instruction-tuned)** 된 LLM을 활용하고, **in-context learning**을 적용하여 검색 및 상태 추적을 수행
		>Are Large Language Models All You Need for Task-Oriented Dialogue?
			- 이 논문은 instruction‑fine‑tuned LLM이 **task‑oriented dialogue (TOD)**—즉, 외부 데이터베이스와의 상호작용을 포함한 **Multi-turn 대화**—에서 얼마나 효과적인지 평가
			- 결과적으로 **belief state tracking** 성능은 전통적 task‑specific 모델에 미치지 못하지만, **정확한 slot 값**을 제공받을 경우, **few‑shot in‑domain 예시**를 활용하면 성공적인 대화 마무리가 가능
	- 사용자 페르소나 모델링(User Persona Modeling)
		![](assets/Pasted%20image%2020250901152319.png)
		- 사용자 페르소나 모델링은 대화 기록을 기반으로 사용자 페르소나를 탐지하고, 각 사용자에 맞춘 맞춤형 응답을 생성한다
		>Personalized Prompt Learning for Explainable Recommendation
		
## 4 LLM Personality Evaluation
- LLM의 성격(personality)이 적응 이후 의도된 페르소나를 정확히 반영하는지를 평가하는 것이다(즉, 지정된 페르소나에 따라 행동하는 역할 수행 LLM과 개별화된 페르소나에 맞춰진 개인화 LLM의 경우)
## 5. Challenges and Future Directions(미래 도전 과제)
- 일반 프레임워크를 향하여
- 장기 맥락 페르소나(Long-Context Personas)
- 데이터셋과 벤치마크의 부족
- 편향(Bias)
	- 기본적으로 모든 학습이 pre-train된 모델에 도메인을 집어 넣는 방식이니 학습이 편향될 수 밖에 없음
	- 이건 사견인데 아직 파인튜닝을 해도 완벽히 도메인에 관련된 답변을 안주는데 당연한거 같음
- 안전성과 프라이버시(Safety and Privacy)
## 6. 광범위한 함의(Broader Implications)
- LLM 개인화가 교육 분야에서 계속 발전함에 따라, 개인은 손쉽게 개인화된 교육 콘텐츠와 강의 자료에 접근하고 저렴한 튜터링을 받을 수 있게 되어, 자원이 제한된 소수 집단에게 큰 혜택을 줄 수 있다. 그러나 특권 계층은 개인 교사를 누리는 반면, 소외된 집단은 LLM 기반 지원에만 접근할 수 있는 **양극화 현상(polarizing trends)** 이 발생할 수 있다는 우려
- 등등
## 7. 결론(Conclusion)
- 페르소나를 활용함으로써, LLM은 맞춤형 응답을 생성하고 다양한 시나리오에 효과적으로 적응할 수 있다.
- LLM 시대의 페르소나 연구를 위해 **역할 수행(Role-Playing)** 과 **개인화(Personalization)** 라는 두 가지 연구 흐름을 요약하였다. 또한 LLM 성격(Personality)을 평가하기 위한 다양한 방법을 제시
- 
### 총평
- 여기서 말하는 페르소나는 되게 광범위하게 정의 하는거 같다. 특히 Role-plaing 부분은 영어를 잘 못하니 좀 이해하기 어려웠다.
	- 결과적으로 Prompteng로 답변을 내기 위해 생각, 행동 하는(대표적으로 React)등 좋은 답변을 이끌기 위해 다양한 행동을 지시하는 거도 LLM의 Persona로 정의한다.
- 되게 중국인이 발표한 논문에 기조가 맞춰져 있는거 같다.
- LLM으로 개인화된 추천 모델을 개발하고 있는데 PALR 이라던지 시작의 방향을 맞출 수 있었다.
- 난 페르소나의 LLM의 캐릭터성 부여에 초점을 두고 읽엇는데 이게 캐릭터성이 아니라 prompt로 다양한 답변을 유도해서 더욱 좋은 결과를 받아보는 방향으로 설명한다.
- 물론 이것도 도움이 되었다.