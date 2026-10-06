#LLM #memory #LLMInference
> 출처
> [Anyscale](https://www.anyscale.com/blog/continuous-batching-llm-inference)
> [Enabling Cost-Efficient LLM Serving with Ray Serve.youtube](https://www.youtube.com/watch?v=TJ5K1CO9Wbs&t=747s)

**백그라운드**
[kv cache](obsidian://open?vault=PARA&file=Area_of_responsibility%2FML%26AI%2F%EC%A0%95%EB%B3%B4_KV%20cache%2FKV%20cache)
## The basics of LLm inference

- 요즘은 LLM 서비스의 속도를 높히는 방법이 대세이다. 그중 가장 메인스트림에 있는 기술중 하나가 Inference 속도 증가이다.
- Inference란 직역하면 추론으로 LLM Inference는 모델이 들어온 문장을 이해하고 답변을 만드는 과정을 말한다.
- 그래서 보통 inference는 LLM의 일련의 작업 과정을 통틀어서 말한다.

![image-20241018101715153](image-20241018101715153.png)

- 위 그림은 가장 기초적이고 전통적인 inference 방식이다.
- 저 그림에서 노란색은 Data의 Input으로 보통 prefix나 prompt 이다.
- LLM은 input이 들어오면 파란색의 output을 만들어 내고 `<EOS>`와 같은 토큰을 만들어 낼 때 까지 계속 이전 토큰을 기반으로 다음 토큰을 예측한다.
- 본 원문 글에서는 BPE(Byte-pair-Encoding)을 예로 들고 있으며 이러한 반복적인 작업에 대한 작업 예시를 설명한다.
  - 입력 prompt가 들어오고 이를 저리하는 단게를 prefil phase라고 한다.즉  prefil phase는 들어온 prompt를 이해하는 작업이다. prefil phase에서는 입력 Prompt 전체의 Attention matrix를 계산하는데 이 matrix는 후속 토큰을 생성하는데 계속 사용이된다. 
  - 이렇게 생성된 prefil phase attention matrix는 후속 토큰을 생성할 때 사용이되고 이 생성해야할 토큰의 길이만큼 기존 토큰의 attention matrix만 구하면된다. prefil phase attention matrix는 서로 독립적으로 계산이 가능하여 병렬적으로 input이 가능한다.
  -  LLM infernce는 [Memory-IO(다른말로 Memory bandwidth)](https://en.wikipedia.org/wiki/Memory_bandwidth)이 성능의 핵심이다(요즘은 저 Attention matrix에 필요한 kv 값을 GPU 메모리에 저장하고 필요할 때 꺼내쓰기 때문). 요즘은 Compute 계산보다 GPU에 정보를 load하는 시간이 더 오래 걸리기 때문에 , high-bandwidth GPU 메모리에 얼마나 큰 batch 사이즈를 설정 가능하냐에 따라 그 속도가 좌우된다. (높은 수준 대역폭인거 보니깐 불러올 때 얼마나 많이 불러오는게 가능한지 인듯)
  - Inference에 필요한 GPU 메모리 양은 기본 모델 크기와 토큰 시퀀스 길이에 따라 확장된다. 만약 13B 크기 모델은 시퀀스의 각 토큰에 대해 약 1MB의 상태를 소모한다고 추정한다. A100 GPU 40GB에서 26GB만큼의 모델 파라미터를 저장한 후 14GB가 남았으므로 한 번에 약 14k 토큰을 메모리에 저장 가능하다. 
  - 꽤 많은 양이 저장된다고 생각이 들수 도 있지만 생각외로 별로 저장을 못한다. 시퀀스 길이를 512로 제한하면 배치에서 최대 28개의 시퀀스를 처리할 수 힜는데 만약 시퀀스 길이가 2048이면 최대 7개의 시퀀스 밖에 처리할 수 밖에 없다.
- 이러한 한계점을 극복하기위해 AutoGPQ와 같은 모델 파라미터의 데이터 정보량을 줄이는(예를 들어 숫자의 메모리 크기가 FP32 인걸 FP8로 바꾸는거)Quantization(양자화) 기법들이 등장한다. 
- 아니면 또다르게 FlashAttention과 같이 어텐션 연산에서 Memory-IO를 덜 사용하게 하는 방식도 있음
- 그래서 이 글은 Continuous batch를 사용하여 모델의 수정 없이 메모리를 더욱 효율적으로 사용하게 만드는 방법을 알려준다.

## LLM batching explained

### Naive batching / static batching

- 먼저 batch(이하 배치) 중 가장 전통적인 방법은 stattic batch라고하는 데 이는 배치의 크기가 infernce가 완료되기 전에는 고정적으로 유지된다. 아래의 그림이 이러한 경우이다.

![image-20241018155806011](image-20241018155806011.png)

- 왼쪽의 그림은 첫 4 개의 시퀸스를 처리할 때 한번의 ireration을 나타내는데 노란색에 해당하는 prompt가 들어오면 대답을 생성하는 파란색 시퀀스 출력을 시작한다. 
- 이렇게 서로 다른 prompt가 들어오면 오른쪽 그림처럼 빨산색의 END 토큰이 나올 때까지 토큰을 계속 생성하는데 이때 할당된 배치크기는 고정이다. 위의 그림처럼 전체 시퀀스의 END가 나올 때 까지 다음 작업에는 돌입하지 않기에 만약 batch크기보다 덜 사용하더라도 그 GPU의 사용률은 충분히 활용된다고 볼 수 없다.
- 이러한 문제는 기존 딥러닝 모델과 달리 LLM Inference에만 해당하는 문제로, 이는 요청이 배치 처리에서 더 일찍 끝나도, 리소스를 해제하고 완료 상태에서 된 서로 다른 시퀀스에 새로운 요청을 추가하기 어렵기 때문이다.
- 모든 입력과 생성 시퀀스들이 같은 길이를 가지면 최고의 GPU 사용률을 이끌어 낸다. 하지만 현실적으로 그게 되지 않는다. 그래서 이러한 문제점을 해결하기 위해서 나온 방법이 Continuous Batch이다.

### Continuous batching

- 그래서 이러한 비효율성을 타개하기 위해사 Orca라는게 나왔다. 
![](assets/Pasted%20image%2020250402211321.png)
- Orca는 하나의 배치안의 모든 시퀸스가 끝나기 기다리는 대신 iteration-level scheduling이라는걸 도입하였다. iteration-level scheduling는 각 Iteration 마다 배치사이즈를 결정한다.( Orca의 iteration-level scheduling가 Continuous batching와 매우 흡사하다.)
- 이러한 방식으로 인해 한 시퀀스가 완성이되면, 배치크기에 비해 작은 시퀀스의 나머지 자리에 새로운 시퀀스를 할당하면서 GPU의 메모리 누수를 줄인다. 이러한 방법은 GPU의 사용률을 더욱 높일수 있다.
- 실제론 위의 그림보다 작업 방식이 더욱 복잡하다. 하지만 저정도로 설명이 되는거 같다.
- Continuous batch는 vllm이나 TGI에서 사용가능하다.
- Continuous batching을 도입하면서 이제 GPU 사용률은 관심사가 아니게 되었다. 이제 throughput을 위해 batch에 담기는 요청의 개수를 늘리는 것이 중요해졌다. 이는 각 요청이 사용하는 메모리 크기에 의해 결정된다.
- 그래서 vLLM의 핵심 기술인 PageAttention이 중요한데 이 개념은 vLLM의 논문을 정리하면서 알아보겠다.

## Contiunuous batching을 지원하는 프레임 워크

- 여기 사진에 있는 프레인워크만 지원하는거는 아니지만 일단 대표적인 것들이다.

![image-20241018213923869](image-20241018213923869.png)

