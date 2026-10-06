**완전한 ‘탈-트랜스포머’는 아직 아니지만, 최근 급부상한 두 축은 (1) SSM 계열(Mamba 등)과 (2) MoE·하이브리드 계열**입니다. 고성능은 여전히 트랜스포머/하이브리드가 주도하지만, **긴 문맥·비용·지연 측면**에서 대안 아키텍처들이 빠르게 치고 올라오고 있어요.

# 지금 주목할 아키텍처들

- **Mamba / Mamba-2 (SSM)**: 어텐션 없이 선형 시간·메모리로 시퀀스 처리. 실제 구현 최적화까지 포함한 Mamba-2가 등장하며 확실한 속도·길이 강점 입증. 긴 컨텍스트·스트리밍에 유리. [arXiv](https://arxiv.org/abs/2312.00752?utm_source=chatgpt.com)[Tri Dao](https://tridao.me/blog/2024/mamba2-part1-model/?utm_source=chatgpt.com)
    
- **Jamba (하이브리드: Transformer + Mamba + MoE)**: 어텐션과 Mamba를 1:7로 인터리브, 주기적으로 MoE 삽입. **256K 컨텍스트**와 높은 처리량을 양립하는 대형 상용 모델 사례. 하이브리드가 현시점 실용 최적해라는 신호탄. [AI21+1](https://www.ai21.com/blog/announcing-jamba/?utm_source=chatgpt.com)
    
- **RetNet (Retentive Network)**: 어텐션 대신 ‘retention’ 메커니즘. **병렬/순환/청크 순환** 3가지 계산 패러다임을 모두 지원해 학습 병렬성과 추론 효율·장문 처리의 균형이 좋음. [arXiv+1](https://arxiv.org/abs/2307.08621?utm_source=chatgpt.com)
    
- **RWKV (RNN계 LLM)**: RNN처럼 순환 상태를 유지하면서도 트랜스포머처럼 병렬 학습 가능. 모바일/엣지 배치에 관심이 큼(최근 v5/6/7까지 진화). [GitHub](https://github.com/BlinkDL/RWKV-LM?utm_source=chatgpt.com)[RWKV 위키](https://wiki.rwkv.com/basic/architecture.html?utm_source=chatgpt.com)[RWKV](https://www.rwkv.com/?utm_source=chatgpt.com)
    
- **Monarch Mixer (M2)**: **시퀀스 길이·모델 차원 모두에서 서브-쿼드라틱**으로 스케일하는 GEMM 기반 구조. 장문 임베딩/검색 등에서 주목. [arXiv+1](https://arxiv.org/abs/2310.12109?utm_source=chatgpt.com)[Hazy Research](https://hazyresearch.stanford.edu/blog/2024-01-11-m2-bert-retrieval?utm_source=chatgpt.com)
    
- **StripedHyena (Hyena 하이브리드)**: 컨볼루션 기반 Hyena 연산자와 트랜스포머를 **grafting** 기법으로 접목한 하이브리드. 긴 문맥·효율 타깃. [Together AI](https://www.together.ai/blog/stripedhyena-7b?utm_source=chatgpt.com)
    
- **Infini-attention (트랜스포머 내 신기술)**: 압축 메모리(+로컬/글로벌 혼합)로 **사실상 무한 컨텍스트**를 지향. ‘대체’라기보다 트랜스포머의 강력한 업그레이드 라인. [arXiv+1](https://arxiv.org/abs/2404.07143?utm_source=chatgpt.com)
    
- **현대 MoE의 재부상 (DeepSeek-V3 등)**: 고활성 파라미터 수를 제한해 **성능/비용 비율**을 크게 끌어올림. MLA(멀티헤드 잠재 어텐션) 같은 트릭과 함께 대규모 모델을 실용화. [arXiv+1](https://arxiv.org/abs/2412.19437?utm_source=chatgpt.com)[GitHub](https://github.com/deepseek-ai/DeepSeek-V3?utm_source=chatgpt.com)
    

# 그럼, 트랜스포머를 “위협”하나?

- **성능 최전선**: 톱티어 범용 품질은 아직 **(하이브리드 포함) 트랜스포머 진영**이 강합니다. 다만 **하이브리드(예: Jamba)**와 **MoE**가 동일 자원 대비 품질·처리량을 크게 개선 중. [AI21+1](https://www.ai21.com/blog/announcing-jamba/?utm_source=chatgpt.com)[arXiv](https://arxiv.org/abs/2412.19437?utm_source=chatgpt.com)
    
- **효율·긴 문맥**: **Mamba/RetNet/RWKV/M2** 등은 **KV 캐시 부담·지연·메모리 사용**에서 이점이 커, **장문 요약·스트리밍·온디바이스**에서 트랜스포머의 급소를 찌르고 있습니다. [arXiv+2arXiv+2](https://arxiv.org/abs/2312.00752?utm_source=chatgpt.com)[GitHub](https://github.com/BlinkDL/RWKV-LM?utm_source=chatgpt.com)
    
- **현실적 전망**: 단기간에 “완전 대체”보단 **하이브리드·MoE·주의(attention) 개선**이 공존하는 다극 체제로 갈 가능성이 큽니다. [AI21](https://www.ai21.com/blog/rise-of-hybrid-llms/?utm_source=chatgpt.com)
    

# 실무 선택 가이드(짧게)

- **초장문/낮은 메모리/높은 처리량**: Mamba(또는 **Jamba 하이브리드**), RetNet 검토. [arXiv+1](https://arxiv.org/abs/2312.00752?utm_source=chatgpt.com)[AI21](https://www.ai21.com/blog/announcing-jamba/?utm_source=chatgpt.com)
    
- **최고 품질·대규모 서비스**: 대형 **Transformer/하이브리드 + MoE(DeepSeek-V3 계열)**. [arXiv](https://arxiv.org/abs/2412.19437?utm_source=chatgpt.com)
    
- **온디바이스/저전력**: **RWKV** 우선 검토, 소형 SSM 실험. [GitHub](https://github.com/BlinkDL/RWKV-LM?utm_source=chatgpt.com)
    
- **연구·파일럿(임베딩/검색/장문)**: **M2, StripedHyena, Infini-attention**로 프로토타이핑. [arXiv+1](https://arxiv.org/abs/2310.12109?utm_source=chatgpt.com)[Together AI](https://www.together.ai/blog/stripedhyena-7b?utm_source=chatgpt.com)
    

원하시면, 현재 쓰시는 워크로드(맥락 길이, 추론 지연 한도, 배포 타깃, 예산)에 맞춰 **아키텍처/모델 후보와 벤치마크 지표**를 바로 뽑아드리겠습니다.