#torch #dnn #nlp #추천시스템 
# PyTorch OOM 트러블슈팅 및 STBA 모델 메모리 최적화 일지

**프로젝트:** 카페 리뷰 속성-의견 쌍(Aspect-Opinion Pair) 추출 STBA 모델 파인튜닝
**주요 이슈:** 스팬(Span) 기반 모델의 $O(N^2)$ 조합 생성으로 인한 VRAM 폭발 및 차원 팽창(Dimension Expansion) 해결

## 1. 1차 OOM: 과도한 스팬(Span) 생성으로 인한 VRAM 초과
- **증상:** `CUDA out of memory. Tried to allocate 17.77 GiB.` (GPU 총 용량 44GB 중 37GB 사용 중 발생)
- **원인:** 스팬 기반 정보 추출 모델은 입력 문장의 모든 토큰 조합(Span)을 생성하고, 이를 1:1로 엮어 관계를 추론함.
    - 토큰 길이가 길고 배치 사이즈가 클수록 내부 연산에 필요한 텐서 크기가 기하급수적으로 증가함.
        
- **해결 방안 (하이퍼파라미터 조정):**
    - `max_length` 축소: 카페 리뷰 도메인 특성상 긴 텍스트가 적으므로, 전처리 시 토큰 최대 길이를 128에서 64로 축소하여 스팬 후보군을 대폭 감소시킴.
    - `batch_size` 축소: DataLoader의 배치 사이즈를 16에서 4로 하향 조정하여 1회 연산량 제한.

## 2. 좀비 메모리 (Zombie Memory) 현상 및 VRAM 관리
- **증상:** 코드 재실행 또는 배치 사이즈 축소 후에도 GPU 메모리가 이미 40GB 이상 점유되어 있어 시작 즉시 OOM 발생.
- **원인:** Jupyter/IPython 환경에서 이전 실행 시 로드했던 모델 객체나, OOM 발생 시 해제되지 않은 텐서 찌꺼기가 VRAM에 잔존함.
    
- **해결 방안:**
    - **OS 레벨 모니터링 및 제어:** 터미널에서 `watch -n 1 nvidia-smi` 또는 `nvtop`을 실행하여 메모리를 점유 중인 프로세스(PID) 확인 후, `kill -9 [PID]` 명령어로 강제 종료.
    - **코드 레벨 캐시 초기화:** 훈련 루프 시작 전 파이토치 캐시 명시적 해제.
        
        
        ```Python
        import gc, torch
        if 'model' in globals(): 
        del model
	    gc.collect()
	    torch.cuda.empty_cache()
        ```
        

## 3. 2차 OOM (4.3TB 할당 요청): 아키텍처 비효율 및 차원 팽창
- **증상:** `CUDA out of memory. Tried to allocate 4318.95 GiB.` (4.3TB의 비정상적인 메모리 할당을 요청하며 프로세스 크래시)
- **원인: `nn.Bilinear` 사용을 위한 물리적 텐서 복사 (Dimension Expansion)**
    - 기존 구조에서는 후보 쌍(Pairs) 간의 관계를 계산하기 위해 파이토치 내장 함수인 `nn.Bilinear`를 사용함.
    - `nn.Bilinear`는 입력되는 두 텐서의 형태(Shape)가 동일해야 연산이 가능함. 이를 맞추기 위해 전체 스팬 벡터 중 필요한 쌍의 인덱스만 추출하여 `[batch_size, num_pairs, hidden_size]` 형태의 거대 텐서를 명시적으로 생성함.
    - 토큰 길이가 128일 때 스팬은 약 700개가 생성되며, 가능한 조합(Pair)은 약 49만 개에 달함. 768차원(RoBERTa hidden_size) 벡터를 49만 번 물리적으로 복사하여 텐서를 구성하는 과정에서, 배치 연산과 기울기(Gradient) 보존이 겹치며 순간적으로 수십 GB ~ 수 TB의 메모리 폭발이 발생함.
        
- **해결 방안: `torch.einsum` 및 매트릭스 브로드캐스팅(Broadcasting) 도입**
    - 명시적인 텐서 복사를 유발하는 `nn.Bilinear` 모듈을 제거하고, 텐서 축약 연산인 `torch.einsum` 기반의 사용자 정의 레이어(`BroadcastBiaffine`)로 아키텍처를 전면 개편함.
    - 조합(Pair) 단위로 벡터를 미리 복사하는 대신, `[batch, num_spans, hidden_size]` 형태의 원본 스팬 텐서 자체를 가중치 매트릭스(`U`)를 거쳐 서로 교차 곱셈(Cross-multiplication)함.
    - 이 연산을 통해 전체 스팬 간의 상호작용을 나타내는 `[batch, num_relations, num_spans, num_spans]` 크기의 '전체 점수판(Full Score Matrix)'을 한 번에 계산함. (이 과정에서의 메모리 소모는 약 18MB 수준으로 극도로 효율적임).
    - 이후 필요한 후보 쌍(`candidate_pairs`) 좌표의 점수만 인덱싱하여 최종 Logit을 반환하는 구조로 변경하여 4.3TB의 메모리 누수를 완전히 해결함.
        

Python

```
# [문제의 코드]: nn.Bilinear를 위한 물리적 차원 팽창 (OOM 발생)
aspect_spans = span_reprs[batch_idx, aspect_indices]   # [batch, 490000, 768] 복사
opinion_spans = span_reprs[batch_idx, opinion_indices] # [batch, 490000, 768] 복사
relation_logits = self.biaffine(aspect_spans, opinion_spans)

# [개선된 코드]: einsum을 활용한 브로드캐스팅 연산 (메모리 최적화)
# 물리적 복사 없이 원본 텐서로 전체 점수판 즉시 생성
interaction = torch.einsum('bsh, rhk, btk -> brst', span_reprs, self.U, span_reprs)
full_relation_matrix = interaction + aspect_context + opinion_context
# 필요한 쌍의 좌표만 인덱싱하여 추출
relation_logits = full_relation_matrix[batch_idx, aspect_indices, opinion_indices, :]
```
