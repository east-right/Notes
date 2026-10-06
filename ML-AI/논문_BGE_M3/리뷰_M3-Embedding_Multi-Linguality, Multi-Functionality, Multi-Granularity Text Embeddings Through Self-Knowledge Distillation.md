#임베딩 #Bert #LLM
> 링크: https://arxiv.org/abs/2402.03216
> 참조링크: [mge-m3 리뷰 블로그](https://velog.io/@jingyeom/BGE-M3-Embedding-Multi-Lingual-Multi-Functionality-Multi-Granularity-Text-Embeddings-Through-Self-%EB%A6%AC%EB%B7%B0)
## 1.Introduce
- 임베딩 모델은 질의와 문서 간의 관련성을 계산하는 IR(Information Retrieval) 작업이나, 각 단어의 출력 임베딩을 통해 단어의 중요도를 추정하는 희소 검색에도 적용된다.
- 하지만 임베딩 모델은 아래와 같은 문제점을 가지고 있다.
	- 영어에만 특화되어 있다.
	- 기존 임베딩 모델은 only 하나의 Task에 맞춰 학습된다.
	- 대부분의 임베딩 모델은 Long document에 성능이 떨어진다.
- 그래서 본 논문에서는 M3-Embedding이란걸 제시한다.
- 이 논문은 아래와 같은 임베딩 모델의 방향을 제시한다.
	- ![](assets/Pasted%20image%2020250202204152.png)
	- 다양한 언어를 지원
	- 언어 교차 리트리버 가능
	- hybrid search 방식과 같이 Self-knowledge distillation 방식으로  각 3가지 함수에서 나온 score를 통합해서 활용함
	- max_token이 길어서 document에도 적용 가능
## 2. Related Work
- 본 논문이 적히기 전까지 최근 임베딩은 3가지 측면을 위해 연구되어 왔다.
	- 일반적인 text Embedding
	- neural Retrieval을 위한 임베딩 모델
	- multi-linguality(다국어) 임베딩
- contrastive learning의 발전으로 인해 Negative Sampling의 성능 향상과, 지금 본 리뷰를 적을 때 기준으로 꽤나 핫한 지식 증류(knowledge distillation)의 활용이 큰 역할을 한다.
- E5, LLM-Embedder등을 비롯한 다양한 임베딩 모델들이 등장을 하였다.
- 임베딩 모델의 가장 큰 주요 응용중 하나는 **neural retrieval**이다. 이는 텍스트 임베딩을 이용해 의미적 관계를 측정함으로, 임베딩 유사성에 기반하여 관련 답변을 검색할 수 있다.(RAG)
- 임베딩 기반 Retrieval에서 사용되는 Retrieval은 3가지가 있다.
	- **Dense Retrieval**
		- ![](assets/Pasted%20image%2020250221152742.png)
		> 출처 : [mge-m3 리뷰 블로그](https://velog.io/@jingyeom/BGE-M3-Embedding-Multi-Lingual-Multi-Functionality-Multi-Granularity-Text-Embeddings-Through-Self-%EB%A6%AC%EB%B7%B0)
		- 텍스트 인코더의 출력을 집계(CLS)와 같은 스페셜 토큰의 값을 받아보거나(이건 BERT분류모델에 관한 내용으로 BERT 분류모델은 가장 문장의 시작을 알리는 토큰인 CLS 토큰의 output 뉴럴값을 기반으로 분류를 진행한다.), MeanPooling을 통해 임베딩 유사성을 계산한다.
	- **Multi-vector retrieval**
		- ![](assets/Pasted%20image%2020250221214728.png)
		> 출처 : [mge-m3 리뷰 블로그](https://velog.io/@jingyeom/BGE-M3-Embedding-Multi-Lingual-Multi-Functionality-Multi-Granularity-Text-Embeddings-Through-Self-%EB%A6%AC%EB%B7%B0)
		- 텍스트 인코더의 출력에 대해 세밀한 상호작용을 적용하여 임베딩 유사성 계산
		- 위 Dense Retrieval과 다르게 특정 토큰 벡터 값을 기준으로 판단하는 것이 아닌, 모든 토큰 벡터를 활용함
		- 모든 토큰을 사용하는 이유는 더욱 정확한 비교를 위해 세밀한 상호작용을 하는데, 이 상호 작용이란 3가지를 진행한다. 각 문서의 내용을 더욱 작은 청크로 나누고, 문서를 요약하고, 문서에 대한 가상의 질문을 생성한다.
		- 저 3가지 방법 중 하나를  사용하거나 복수 개를 사용하여 유사도 계산에 사용하는 방식을 말한다. 대표적으로 [[리뷰_Precise Zero-Shot Dense Retrieval without Relevance Labels]]에서 제안한 HyDe 방식이 있다.
	- **Sparse retrieval**
		- 텍스트의 단어들을 TF-IDF와 같은 희소 밀집 벡터 값으로 변환 혹은 lexical retrieval(어휘 검색) 등을 통해 두 문장을 비교하여 유사성 판단
		- TF-IDF 기반의 대표적인 방식은 BM25이다. 이건 알고리즘 방식이다.
		- ![](assets/Pasted%20image%2020250223183921.png)
		> 출처 : [mge-m3 리뷰 블로그](https://velog.io/@jingyeom/BGE-M3-Embedding-Multi-Lingual-Multi-Functionality-Multi-Granularity-Text-Embeddings-Through-Self-%EB%A6%AC%EB%B7%B0)
- 위와 같은 3가지 방법이 메인인데 본 논문이 적힐 때 까지만 해도 위 3가지 방식을 통합한 모델과 방식이 존재하지 않았다.
- 그리고 본 논문 작성자가 중국인이라 그런가 대부분의 임베딩 모델이 영어에 치우쳐져 있는걸 많이 강조한다. 나 또한 동의한다. 그래서 다국어 텍스트 인코더 모델이 필요하다고 느낀다.
## 3. M3-Embedding
- 쿼리가 들어왔을 때 Corpus에서 가장 유사도가 높은 문서를 찾아오는 3가지 방법론을 활용
- 언어가 달라고 사용가능하게 만듬
### 3.1 Data Curation
- 본 논문에선 3가지의 데이터를 준비한다.
	- Unsupervised data
	- Finetuning data(supervised data)
	- synthetic data(합성 데이터)
		- 위키피디아 문서와 다른 데이터셋을 샘플링 한 다음 GPT를 통해서 질문과 답변을 만들어서 파인 튜닝 데이터에 사용
### 3.2 Hybrid Retrieval
- BGE-M3는 **Dense retrieval,  Lexical Retrieval,  Multi-Vector Retrieval** 3가지의 socre를 사용하여 Retrieval 한다.
- **Dense retrieval**(Dense Embedding)
	- 모델을 타고 나온 CLS의 hidden_State 토큰 값을 사용
	- 이렇게 나온 값을 norm 한다음에 두 문서간의 내적 값으로 두 문서의 유사도를 판단
	- **즉 단일 벡터에 대한 임베딩 유사도 값을 구한다.**
- **Lexical Retrieval**(Sparse Embedding)
	- 용어 기반으로 두 문서간의 유사도를 구함
	- 수식을 보니깐 특정 문자가 문서에서 얼마나 자주 등장했는 지를 실수 값으로 나타내고, 높은 가중치를 부여한다.
	- 이렇게 나온 쿼리(q)와 패시지(p, 문서)는 the joint importance of the co-existed terms (denoted as q ∩ p)을 통해서 계산된다. 아마 가중치가 높은 단어(토큰)이 얼마나 겹치는 지 수치화를 시킨 계산법인거 같다.(TF-IDF?)
	- **즉 단어기반 희소 표현 유사도 값을 구한다.**
- **Multi-Vector Retrieval**(ColBERT)
	- Dense랑 다르게 쿼리와 패시지의 hidden_state를 빠져나온 모든 vector 값을 사용함
	- **late-interaction([[ColBERT, ColBERTv2]]]를 사용하여 아주 세밀한 유사도 점수를 계산한다.**
- 이렇게 각각의 방법론을 활용하여 후보 결과를 추출하고 다시 rerank(cross distillation)하여 최종 결과를 뽑는다.(Self-Knowledge Distillation)

### 3.3 Self-Knowledge Distillation
![](assets/Pasted%20image%2020250223201930.png)
- 일반적인 손실 함수를 사용한 학습은 서로 다른 검색 목표와 방법을 지진 모델들이 충돌할 가능성이 높기에 성능 저하가 우려된다.
- 그래서 self-knowledge distillation를 활용하여 학습 과정을 통합할 것 이다.
	1. 각각의 방법론을 통해 나온 결과값을 일단 더한다.(일단 weight는 생각하지 말자)
		![](assets/Pasted%20image%2020250223202904.png)
	2. Loss 값도 간단하게 모든 값을 더한다.
		![](assets/Pasted%20image%2020250223203128.png)
	3. 이제  1번의 s_inter를 teacher로 사용하여 아래와 같이 loss Function을 수정한다. s* 은 s_dense, s_lex, s_mul 전부 다 올 수 있다. p는 softmax 함수이다.
		![](assets/Pasted%20image%2020250223204146.png)
- 최초 M3의 학습은 두 번으로 이루어져 있다. 대규모 unsupervised data를 활용하여 pre_train한다. 이 단계에서는 Dense Retrieval만 학습니다.
- 그런 다음 Self-Knowledge Distillation을 활용한 fine-tuning을 해서 최종 학습을 진행했다.

### 3.4 Efficient Batching
![](assets/Pasted%20image%2020250223204641.png)
- 원래 가장 좋은 방법은 가능한 큰 batch에 많은 negative를 넣는게 성능이 좋다.
- 한 배치에 너무 많은 data가 들어가면 GPU 메모리와 연산 능력의 제한 때문에 불가능하다. 그래서 적절한 batch 구성이 필요하다.
	-  시퀀스 길이에 따라 data 그룹화
		- 자동 padding 연산량 감소
	- long 시퀸스에선 한 배치에 여러 sub-batch를 구성
		- gradient checkpointing을 활용하여 각 서브 배치가 끝나면 순차적으로 결과들을 인코딩하여 결과를 도출한다. 즉 한 배치를 나눠서 결과가 나오면 다음 서브 배치에 이전 결과 값을 받아 진행하는 방법이다.
	- 서로 다른 GPU로부터 생성된 임베딩을 모두 브로드캐스트(broadcast)하여 각 디바이스가 전체 임베딩을 획득
		- 이를 통해 in-batch negative 샘플의 다양성을 확보하고, contrastive learning의 효과를 극대화
## 4. Experiment
![](assets/Pasted%20image%2020250223205439.png)
![](assets/Pasted%20image%2020250223205449.png)
![](assets/Pasted%20image%2020250223205504.png)
## 5. Conclusion
- 결론적으로 M3 Embedding은 self-knowledge distillation, 효율적인 배치, 고품질 데이터 생성 방안에 대한 기술적 기여를 한다.
- 다국어 검색, 교차 언어 검색 도 지원하고 long-document에도 영어와 중국어를 제외한 언어에도 좋은 성능을 보인다.
- 하지만 우려 사항도 있다.
	- 일반화 가능성
		- 결과가 벤치마크 데이터에 너무 기운게 아닐까
	- long text 처리
		- max_token을 넘어가는 문서에는 부담이 가해짐
	- 언어 지원 편차
		- 많은 언어를 지원하지만 언어에 따라 성능의 차이가 확연함
## 6. Review
- 각기 다른 3가지 방법론의 모델의 결과를 총합하여 최종 결과를 내놓는 방식은 ML의 RF와 비슷한거 같다. 역시 좋은 결과를 얻기 위해서는 다양한 모델의 결과를 받아보는게 최고인거 같다.
- 나도 맨 처음에 각각의 방식이 다른데 그럼 3가지 방법론에 대해 다른 loss를 줘서 학습하고 하면 메모리가 남아나지 않을거라 생각했는데 self 지식 증류는 매우 좋은 아이디어라고 생각한다.
- 지식 증류가 핵심인게 아직 개념은 많이 잡히지 않았지만 모니까 gamma2를 통한 지식 증류 BGEm-3모델도 존재하는거 보면 이 지식증류가 무었을 하는건지 바로 알아봐야겠다.