[원문]([https://arxiv.org/pdf/2510.26658](https://arxiv.org/pdf/2510.26658?fbclid=IwZXh0bgNhZW0CMTAAYnJpZBExeWNtSDF6dWlEZTRCRjFHMnNydGMGYXBwX2lkEDIyMjAzOTE3ODgyMDA4OTIAAR411f_hCHmVlDzMIfLK6ci2GnrTXZRKVVGzJr2_y0iw-vZbir9DU_ggzA24Gw_aem_-PWYydA3d4UFG5lU2j9MzA))

10월 30일자, Microsoft Research 가 발표한 <The Era of Agentic Organization: Learning to Organize with Language Models> 논문은,

LLM이 단일 개체로 사고하던 기존 방식에서 벗어나, 여러 내부 에이전트가 협력적으로 사고하는 ‘Agentic Organization’ 개념을 제안했습니다.

핵심 개념은 AsyncThink(Asynchronous Thinking) 입니다.

AsyncThink는 하나의 모델 안에서 Organizer 와 작업자Worker 를 가상적으로 설정하고, 이들이 Fork–Join 구조를 통해 병렬적으로 사고하도록 만듭니다.(그림을 봐야 좀더 이해가 쉽습니다..)

Organizer 는 문제를 분석하고 하위 과제를 나누며, worker 들은 각각의 sub-query를 동시에 해결한 후 결과를 반환합니다.

이후 조직자가 결과를 종합하여 최종 답을 도출합니다.

이러한 구조는 하나의 뇌가 여러 신경망을 동시에 활성화하여 병렬로 사고하는 것과 유사합니다.

실험 결과 AsyncThink는 기존 Sequential Thinking보다 추론 속도를 약 28% 단축하면서도 정확도를 유지하거나 향상시켰습니다. 특히 수학적 추론, Sudoku, 멀티 솔루션 퍼즐 등에서 우수한 성능을 보였습니다.

즉, 이 논문은 “사고를 효율적으로 조직하는 모델”을 구현해서 LLM의 다음 패러다임으로 제시했습니다.

그저께 올린 Alibaba의 Tongyi DeepResearch 는 AsyncThink와 달리, ‘탐사형(agentic) LLM’ 즉, 스스로 정보를 탐색하고 검증하는 모델입니다.

두 모델 모두 ‘에이전틱(Agentic)’이라는 공통분모를 가지지만, 지향점은 또 다른거 같습니다.

AsyncThink가 “사고를 조직화하는 모델” 이라면, Tongyi는 “탐색하고 조사하는 모델” 입니다.

Tongyi는 질문을 받으면 즉시 답하지 않고, 스스로 탐색 경로를 설계하며 웹, 문서, 데이터베이스를 활용해 가설을 세우고 검증한 뒤 보고서를 작성합니다.

이는 단순한 RAG나 웹검색 기반 모델이 아니라, ‘조사 행위 자체’를 학습한 모델이죠.

반면 AsyncThink는 외부 탐색이 아닌 내부 사고의 구조화에 초점을 둡니다. LLM 내부의 사고를 병렬적으로 나누고 합치는 과정을 강화학습을 통해 학습합니다.

이 두 연구는 공통적으로 “LLM이 단순한 답변 기계에서 자율적 사고 체계로 진화하고 있다”는 점에서 LLM의 다음 스테이지를 기대하게 합니다.

기존 LLM은 사용자의 명령에 반응적으로 답하는 Instruction Follower였다면, AsyncThink와 Tongyi는 이 구조를 바꾸어, LLM이 질문을 해석하고 구조화하며 스스로 탐색하는 존재로 발전했습니다.

이 변화는 단순한 기능적 업그레이드가 아니라, 인공지능의 인지 구조 자체를 재설계한걸로 봐도 되지 않을까 싶습니다.

게다가,

AsyncThink와 Tongyi는 모두 모델 크기를 키우지 않고도 정확도를 향상시켰습니다. ‘정확한 사고 절차’ 로 정확도를 높인거죠.

AsyncThink는 Fork–Join 구조를 통해 사고의 병렬성을 높였고, Tongyi는 Mid-training을 통해 탐색의 루프 구조를 내재화했습니다. LLM이 자기 검증능력을 가진 지능적 시스템으로 진화하고 있음을 보여줍니다.

GraphRAG 가 (외부 지식)을 구조화하고,

AsyncThink가 (내부 사고)를 조직하며,

Tongyi가 그 위에서 (정교한 탐색)을 수행한다면,

단순히 지식을 암기하는 존재를 넘어, 스스로 배우고 사고하는 존재, 외부 보상없이도 학습하는 자율 연구형 AI의 형태가 되지 않을까요.