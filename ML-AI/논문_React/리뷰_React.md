#Agent #LLM #Prompt #CoT
> 논문링크: https://arxiv.org/abs/2210.03629

## 들어가기
좀 많이 늦은감이 없지 않게 있는 React 리뷰이다. 기본적으로 CoT 기반 방식으로 현 리뷰를 작성하는 타이밍에서는 LLM의 Agent의 핵심적인 부분으로 React가 사용된다.
이 React의 생각과 행동은 추후 FunctionCalling 기술과 합쳐져서 LLM Agent의 핵심으로 자리 잡는다.
결국 지금 대중적으로 React 방식이라고 하면 **React Prompting 방식을 사용한 FunctionCall 이라고 생각하면 된다.**

## 1. Introducde
- 인간은 어떤 두 가지 행동 사이에 과정을 이해하거나 예외 상황에 따라 계획을 조절하는 등 인지적 추론을 거친다.
- 반대로 이런 추론에 의해 도달한 결론을 뒷받침하기 위해 실제로 어떤 행동을 하기도 한다.
- 이처럼 acting과 reasoning의 시너지를 통해 인간은 Task를 빠르게 학습하고 새로운 문제에 대해 추론하고 결정한다.
- 위와 같은 방법을 최근 LLM에도 적용한다.(이게 아마 CoT 말하는 듯)
- Prompting을 잘하면 LLM은 단계적 추론 과정을 통해 학습하지 않은 과제도 수행할 능력을 가지게 된다.
- CoT 과정은 모델의 행동을 정적으로 제한하고 내부 학습 데이터에 기반하지 외부 데이터를 활용하지 못하여 할루시네이션을 유발한다.
- 최근에는 Pre-train된 모델에 environment을 사용하여 성능을 높히는 방법으로 연구되는 중이다.
- 이러한 접근법은 멀티모달 데이터를 텍스트로 변환하여, 모델이 특정 도메인에 대한 행동이나 계획을 생성하고, 컨트롤러가 이를 실행하거나 선택하는 방식으로 구성
- 하지만 이러한 접근법은 언어 모델의 추론 과정을 전혀 고려하지 않는다.
- 즉 추론(CoT)과 행동(Act, 외부 도구 사용, 혹은 정보 제공)을 둘 다 동시에 사용해야 한다.
- 본 논문에선 추론과 의사 결정 문제를 해결하기 위해 reasoning과 acting을 동시에 언어 모델에 결합하는 패러다임인 ReAct를 제안한다.
- ReAct Prompting은 LLM이 추론 과정과 작업 행동을 함께 생성하여 모델이 높은 수준의 행동 계획을 구성하는 동시에 Wikipedia와 같은 외부 환경과 상호 작용하게 만들고 추론 내용에 포함될 수 있게 만든다.
- 이 뒤는 벤치마크 내용과 이 논문이 뭐에 기여했는지 블라블라 적어 놓음
## 2. ReAct: Synergizing Resoning + Acting
![](assets/Pasted%20image%2020250114132425.png)
```
질문 콜로라도 조산 운동의 동쪽 구역이 확장되는 지역의 표고 범위는 얼마이니?
생각 1 콜로라도 조산 운동을 검색하여, 콜로라도 조산 운동의 동쪽 구역이 확장되는 지역을 찾은 다음, 그 지역의 표고 범위를 찾아야 해.
행동 1 검색하기[콜로라도 조산 운동]
관찰 1 콜로라도 조산 운동은 콜로라도와 그 주변 지역에서 산이 형성되는 과정(조산 운동)을 말해.
생각 2 동부에 대한 언급이 없어. 그래서 동부 섹터를 찾아봐야겠어.
행동 2 찾아보기[동부 섹터]
관찰 2 (결과 1 / 1) 동쪽 섹터는 고원 지대까지 뻗어 있고 중앙 평원 조산 운동이라고 불려.
생각 3 콜로라도 조산 운동의 동쪽 섹터는 고원 지대까지 뻗어 있어. 따라서 고원 지대를 검색하여 해발 범위를 찾아야 해.
행동 3 검색[고원 지대]
관찰 3 고원 지대는 두 개의 다른 지대 중 하나를 가르켜.
생각 4 대신 고원 지대(미국)를 검색해야해.
행동 4 검색[고원 지대 (미국)]
관찰 4 고원 지대는 대평원\(the Great Plains\)의 일부 지역이야. 동쪽에서 서쪽으로, 고원 지대는 약 1,800피트에서 7,000피트(550~2,130m)까지 고도가 높아져.[3]
생각 5 고원 지대의 해발 고도는 약 1,800~7,000피트이므로, 정답은 1,800~7,000피트야.
행동 5 완료[1,800~7,000피트]
...
```
- Environment와 상호작용해서 Task를 해결하는 일반적인 Agent를 기준으로 설명한다.
- 각 step에 Agent는 Env에 대한 관측(observe, o)을 통해 행동(a)를 취한다. **이 과정에서 Agent의 정책(Policy)은 문맥(context)가 주어 졌을 때 기대되는 행동(a)라고 정의한다.**
- ReAct는 아이디어는 간단하다. Agent의 기존 Action space를 space of Language를 더해서 확장시키는데 Space of Language안에서 이루어 지는 행동(a)은 thought 혹은 reasoning trace라고 말한다. 
- 위 예시Prompt를 보면 thought 부분이 결국 기존 RL과 달리 행동이 'action'에 대한 행동만 있는게 아니라 thought라는 action을 하기 전 context 형식으로 생각을 하는 부분이 존재하는 걸 확인 가능하다.
- 그럼 논문에서 정책(policy)가 $\pi(a_t\|c_t)$ 를 따른다고 되어 있는 데 Thought의 행동이 선택 되면 이에 기반해 action의 여러가지 행동 중 앞의 Thought의 행동과 연관된 행동을 수행한다는 뜻이다.
- 즉 위 한글로 된 예시를 자세히 들여다 보면 `행동 1 검색하기 [콜로라도 조산 운동]`이라는 action의 행동이 나온 이유가 앞의 thought의 행동(context)의 내용이 `콜로라도의 조산 운동을 검색하여 ....`이런 내용이 나왔기 때문이다.
- 즉 위 말을 풀어서 설명을 하면 LLM Agent이 행동을 하기 전에 추론 내용을 CoT 처럼 입력을 하여 Action의 선택지를 넓히고 보다 정확한 행동을 유도한다.
- Thought는 외부 환경(external environment)에는 영향을 미치지 않기에 관측(obs)에 대한 피드백은 발생하지 않는다.
- 대신 next step의 thought의 행동은 현재 obs의 문맥을 기반으로 추론하여 적절한 정보를 구성하여 다음 thought의 행동을 결정한다.($c_{t+1}=(c_t,\hat{a}_t$))
- 위 말은 예시들 딱 봤을 때 생각 2의 구성 방식은 앞선 관찰 즉 관찰 1의 context를 보고 이번 step에 행동 2에 영향을 미칠 적절한 생각 2 context를 구성한다는 뜻 이다.
- space of Language는 무한하다. 그래서 Action space 구성의 context에 해당하는 내용만 학습하기에는 성능이 좋지 한다. 그렇기에 언어에 대한 많은 학습이 이루어진 LLM모델이 필요하다.
- 논문에서는 Few-shot in-context example Prompt형식을 사용하는 LLM PaLM-540B를 가지고 작성한다. In-Context example는 사람이 작성한 Actions, Thoughts, Observations를 포함한다. 
>[!독백] 
>글을 한번 싹 쓰고 다 지우고 다시 쓴다. Agent를 언급하면서 Enviroment 혹은 policy 등등 이 보니까 강화학습과 연관된 내용과 개념들이 더라 한번 싹 읽고 다시 읽어보니 이제야 이해가 간다. 맨 처음에는 무슨 소리를 하는 건지 몰랐는데 [[OpenAI Spinning UP RL(reinforcement learning)]]이 글은 강화학습의 기본적인 용어와 개념을 담고 있다. 읽으면 이해가 된다.

- ReAct는 Agent의 의사결정 및 추론 능력이 LLM과 합쳐졌기에 독특한 특징이 존재한다.
	1. 직관적이고 설계가 쉽다.(Intuitive and easy to design)
		- 설계자가 CoT 형식처럼 생각을 적어 내려 가면서 설계
	2. 일반적(Generak)이고 유연한(flexibil)
		- 다양한 task에 적용 가능
	3. 좋은 성능을 보여주고 견고하다(Performant and robust:)
		- 새로운 Task가 들어와도 안정적인 결과 보장
	4. 인간적이고 제어 가능(Human aligned and controllable)
		- ReAct의 결과는 추론 단계의 context 작성 때문에 해석하기 용이하다
## 3. KNOWLEDGE-INTENSIVE REASONING TASKS
### 3.1 Setup
#### Domain
- multy-hop 질답 문제
	- Hot-PotQA Benchmark dataSet 사용 
- 사실 검증 문제
	- FEVER Benchmark dataSet 사용
- 두 Task에 모두 question-only 설정으로 작업한다. 본 설정에서는 LLM한테 질문 이외에 Prompt의 답변을 위한 다른 지원을 제공하지 않고 미리 학습된 내부 지식과 외부 환경(Wikipidia API)만 사용하여 답변을 생성한다.
#### Action Space
본 논문에선 외부 환경의 정보 검색을 위해서 3 가지의 Action을 지원하는 Wikipidia API를 설계
1. `Search[entity]`
	- 관련 wiki 페이지가 존재한다면 corresponding entity로부터 첫 5개의 문장을 반환
2. `lookup[string]`
	- 페이지 내에 'string'을 포합하고 있는 문장의 다음 문장을 반환
3. `finish[answer]`
	- 현재 Task를 answer로 마무리
### 3.2 Methods
#### ReAct Prompting
HotPotQA와 FEVER 각각에서 6개, 3개의 학습 데이터를 임의로 선택하여 few-shot example로 사용, 이것들은 벤치마크 검증시에 사용되지 않고 prompt에 사용됨, 그림 1과 같이 논문에서 말하는 Prompt에는 Thought, Action, Observation이 포함되어 있다.
#### Baseline
비교 분석할 Prompt는 총 4가지 이다.
- Standard Prompt: thoughts, actions, observations 없이 일반적인 Prompt
- CoT
- CoT-SC
- Acting-only Prompt: Thought는 제외 즉 추론은 안함
#### Combining Internal and External Knowledge
ReAct가 더욱 사실적이고 답변의 근거가 명확하다. CoT는 추론 구조를 잘 쌓지만 Hallucinated가 존재한다. 그래서 두 가지 방식을 휴리스틱하게 ReAct와 Cot-SC 두 개를 결합하여 사용해 보자. ReAct>CoT-SC와 CoT-SC>ReAct 둘 다 사용
#### FInetuning
먼저 ReAct 방식의 데이터 셋을 만들어야 하기에 [[리뷰_STaR{ Bootstrapping Reasoning With Reasoning]]와 비슷한 방식을 채택하여 질문을 주고 큰 모델에게 ReAct 형식의 정답 생성 궤적(Trajectory)와 답변을 생성하게 지시, 개중 정답을 맞춘 3000개의 궤적과 답변을 우리가 학습 시킬 모델인 PaLM8/62B 모델에 파인튜닝 데이터로 사용한다.
### 3.3 Result and Observations
![](assets/Pasted%20image%2020250115143816.png)
- ReAct outperforms Act consistently
    - 기본저으로 ReAct가 Act에 비해 두 벤치마크에서 더욱 좋은 성능을 보여줌
    - fine-tuning 결과 또한 reasoning traces가 informed acting에 도움이 된다는 것을 보여준다.
- ReAct vs. CoT
    - Fever에서는 ReAct가 CoT를 앞섰으나 HotpotQA에서는 약간 뒤처짐 두 비교군의 관찰 사항은 아래와 같다.
	    - A) CoT의 할루시네이션은 아직 문제다.(Hallucination is a serious problem for CoT)
	    - B) ReAct의 방식은 groundedness & trustworthiness 성능 향상에는 도움이 되지만, reasoning step을 formulating하는 데의 flexibility는 줄어듦
	    - C) ReAct의 경우 informative knowledge를 성공적으로 retrieve하는 것이 매우 중요(외부 환경에서 성공적으로 검색하는게 중요)
![](assets/Pasted%20image%2020250115145016.png)
- _**ReAct + CoT-SC perform best for prompting LLMs**_
    - 모델의 internal & external 지식을 적절히 혼합하는 것이 reasoning tasks에서 중요
    - 테이블 1을 보면 알겠지만 본 방식이 제일 효과가 좋았음
    - 이 실험 결과를 통해 모델이 갖고 있는 내부 지식과 검색을 통해 사용할 수 있는 외부 지식을 적절히 결합하는 것의 중요성을 알 수 있다.
![](assets/Pasted%20image%2020250115150128.png)
- ReAct performs best for fine-tuning
    - PaLM-8/62B 사이즈에서는 prompting ReAct의 성능이 네 방식 중 가장 낮음
    - 그러나, 3,000개의 예시로 fine-tuning할 때는 ReAct가 가장 효과적인 방식임이 확인됨
    - 즉  작은 규모의 모델에서는 ReAct가 좋은 성능을 보여주지 못하지만 파인튜닝을 진행을 하고나면 가장 좋은 방안이 된다. 
    - Standard 또는 CoT는 ReAct와 Act에 비해 fine-tuning 성과가 좋지 않음
    - 사람이 작성한 데이터가 많을 수록 fine-tuning하는 것이 ReAct의 잠재력을 최대한으로 끌어내는 방안
## 4. Decision Making Task
본 논문은 ALFWorld와 WebShop에서 ReAct를 테스트한다..이 두 데이터는 모두 Agent가 긴 시간 동안 Action해야 하고 보상이 희소하여, 효과적으로 행동하고 탐색하기 위해 추론이 필요합니다.
- ALFWorld  
    - agent가 high-level goal을 달성하기 위한 6개 종류의 태스크로 구성
    - text actions을 통해 가상의 집을 navigating and interacting 
    - 각 종류의 태스크에 대해 세 개의 trajectories를 랜덤하게 annotate
    - 목표를 decompose → subgoal completion을 track → next subgoal을 결정 → commonsense를 활용한 reasoning
- WebShop
    - 1.18M real-world products & 12k human instructions
    - high variety of structured and unstructured texts
    - 500개의 test instructions에 대한 평균 스코어로 평가
- Results
    - ReAct가 Act에 비해 두 데이터셋에서 좋은 성능을 보임
    - Act는 주로 goal을 subgoal로 decompose하지 못함
    - Inner Monologue 방식 역시 ReAct에 비해 열등함
![](assets/Pasted%20image%2020250115152148.png)
## 후기
본 리뷰를 작성할 땐 외부 Tool을 이용하는 LLM Agent 방식이 대세이다. ReAct를 기반으로 Function Calling등 이 존재하는 데 현 시점에선 본 논문의 작성 시점 보다 LLM의 성능이 높아져 Function Calling만을 많이 사용하는 추세인거 같다.

하지만 본 논문의 내용은 외부 Tool을 활용하면서 RL 기반의 Agent 학습법과 CoT를 결합하여 모델이 보다 정확한 내용을 준다는 외부 tool 이용에 관한 내용이라 그 의의가 높다.

논문을 읽다보니 생각이 든건데 LLM의 문장 생성 능력으로 어떠한 결과를 보고 판단을 내린다는 것이 참 대단한거 같다. 추론 행동 나오는 논문을 읽을 때 마다 그냥 임베딩 된 숫자 벡터를 보고 다음 단어를 예측한다는 행위가 이 정도로 모든 생각의 근간이 된다는 게 무서울 따름이다.

또한 적절한 외부tool을 선택하게 만드는 prompting 기술이 LLM 성능향상의 키가 될 것 이다.

## 추가 Refernce
https://basicdl.tistory.com/entry/%EB%85%BC%EB%AC%B8%EB%A6%AC%EB%B7%B0-ReAct-Synergizing-Reasoning-and-Acting-in-Language-Models

https://chanmuzi.tistory.com/452