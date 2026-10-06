# AI Engineering Notes

LLM·RAG·Agent·검색·DB를 공부하며 직접 정리한 노트입니다. Obsidian으로 작성했습니다.

## 구성

| 폴더 | 내용 | 노트 수 |
|---|---|---|
| [ML-AI](ML-AI) | LLM·검색·추천 관련 개념 정리와 논문 리뷰 | 63 |
| [강의노트_RAG-LLM-Agent](%EA%B0%95%EC%9D%98%EB%85%B8%ED%8A%B8_RAG-LLM-Agent) | RAG·LLM·Agent 강의 수강 정리 노트 | 13 |
| [세미나_Lablup-2025](%EC%84%B8%EB%AF%B8%EB%82%98_Lablup-2025) | Lablup 2025 세미나 정리 (LLM 서빙·추론 최적화) | 7 |
| [온톨로지-지식그래프](%EC%98%A8%ED%86%A8%EB%A1%9C%EC%A7%80-%EC%A7%80%EC%8B%9D%EA%B7%B8%EB%9E%98%ED%94%84) | 온톨로지·Neo4j·Symbolic AI 정리 | 7 |
| [DB](DB) | Oracle 구조·파티션·튜닝, 마이그레이션 정리 | 11 |
| [Docker-K8s](Docker-K8s) | Docker 이미지 최적화·배포 정리 | 6 |
| [Python](Python) | Python 내부 동작·동시성 정리 | 12 |
| [OS-알고리즘](OS-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98) | OS·리눅스·자료구조 정리 | 14 |
| [수학-통계](%EC%88%98%ED%95%99-%ED%86%B5%EA%B3%84) | 선형대수·확률 개념 정리 | 8 |
| [Git](Git) | Git 협업 방식 정리 | 2 |

## 논문 리뷰

- [리뷰_LLM orchestration 기술을 활용한 고객센터 에이전트 개발 사례_SK텔레콤 설용수](ML-AI/%EB%A6%AC%EB%B7%B0_LLM%20orchestration%20%EA%B8%B0%EC%88%A0%EC%9D%84%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EA%B3%A0%EA%B0%9D%EC%84%BC%ED%84%B0%20%EC%97%90%EC%9D%B4%EC%A0%84%ED%8A%B8%20%EA%B0%9C%EB%B0%9C%20%EC%82%AC%EB%A1%80_SK%ED%85%94%EB%A0%88%EC%BD%A4%20%EC%84%A4%EC%9A%A9%EC%88%98.md)
- [리뷰_M3-Embedding_Multi-Linguality, Multi-Functionality, Multi-Granularity Text Embeddings Through Self-Knowledge Distillation](ML-AI/%EB%85%BC%EB%AC%B8_BGE_M3/%EB%A6%AC%EB%B7%B0_M3-Embedding_Multi-Linguality%2C%20Multi-Functionality%2C%20Multi-Granularity%20Text%20Embeddings%20Through%20Self-Knowledge%20Distillation.md)
- [Chain-of-Thought Prompting Elicits Reasoning in Large Language Models](ML-AI/%EB%85%BC%EB%AC%B8_Chain-of-Thought%20Prompting%20Elicits%20Reasoning%20in%20Large%20Language%20Models/Chain-of-Thought%20Prompting%20Elicits%20Reasoning%20in%20Large%20Language%20Models.md)
- [DPO(Direct Preference Optimization)](ML-AI/%EB%85%BC%EB%AC%B8_DPO%28Direct%20Preference%20Optimization%29/DPO%28Direct%20Preference%20Optimization%29.md)
- [리뷰_Efficient Memory Management for Large Language Model Serving with PagedAttention](ML-AI/%EB%85%BC%EB%AC%B8_Efficient%20Memory%20Management%20for%20Large%20Language%20Model%20Serving%20with%20PagedAttention/%EB%A6%AC%EB%B7%B0_Efficient%20Memory%20Management%20for%20Large%20Language%20Model%20Serving%20with%20PagedAttention.md)
- [[리뷰]FINETUNED LANGUAGE MODELS ARE ZERO-SHOT LEARNERS](ML-AI/%EB%85%BC%EB%AC%B8_FINETUNED%20LANGUAGE%20MODELS%20ARE%20ZERO-SHOT%20LEARNERS/%5B%EB%A6%AC%EB%B7%B0%5DFINETUNED%20LANGUAGE%20MODELS%20ARE%20ZERO-SHOT%20LEARNERS.md)
- [FINETUNED LANGUAGE MODELS ARE ZERO-SHOT LEARNERS](ML-AI/%EB%85%BC%EB%AC%B8_FINETUNED%20LANGUAGE%20MODELS%20ARE%20ZERO-SHOT%20LEARNERS/%5B%EB%A6%AC%EB%B7%B0%5DFINETUNED%20LANGUAGE%20MODELS%20ARE%20ZERO-SHOT%20LEARNERS.textbundle/FINETUNED%20LANGUAGE%20MODELS%20ARE%20ZERO-SHOT%20LEARNERS.md)
- [GLU(Gated Linear Unit)](ML-AI/%EB%85%BC%EB%AC%B8_GLU/GLU%28Gated%20Linear%20Unit%29.md)
- [리뷰_GQA_Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints](ML-AI/%EB%85%BC%EB%AC%B8_GQA%20Training%20Generalized%20Multi-Query%20Transformer%20Models%20from%20Multi-Head%20Checkpoints.textbundle/%EB%A6%AC%EB%B7%B0_GQA_Training%20Generalized%20Multi-Query%20Transformer%20Models%20from%20Multi-Head%20Checkpoints.md)
- [[간단리뷰]Generated Knowledge Prompting for Commonsense Reasoning](ML-AI/%EB%85%BC%EB%AC%B8_Generated%20Knowledge%20Prompting%20for%20Commonsense%20Reasoning/%5B%EA%B0%84%EB%8B%A8%EB%A6%AC%EB%B7%B0%5DGenerated%20Knowledge%20Prompting%20for%20Commonsense%20Reasoning.md)
- [Generated Knowledge Prompting for Commonsense Reasoning](ML-AI/%EB%85%BC%EB%AC%B8_Generated%20Knowledge%20Prompting%20for%20Commonsense%20Reasoning/%5B%EA%B0%84%EB%8B%A8%EB%A6%AC%EB%B7%B0%5DGenerated%20Knowledge%20Prompting%20for%20Commonsense%20Reasoning.textbundle/Generated%20Knowledge%20Prompting%20for%20Commonsense%20Reasoning.md)
- [HNSW(Hierarchical Navigable Small World graphs)](ML-AI/%EB%85%BC%EB%AC%B8_HNSW%28Hierarchical%20Navigable%20Small%20World%20graphs%29/HNSW%28Hierarchical%20Navigable%20Small%20World%20graphs%29.md)
- [Hierarchical Neural Story Generation](ML-AI/%EB%85%BC%EB%AC%B8_Hierarchical%20Neural%20Story%20Generation/Hierarchical%20Neural%20Story%20Generation.md)
- [리뷰_Precise Zero-Shot Dense Retrieval without Relevance Labels](ML-AI/%EB%85%BC%EB%AC%B8_Hyde/%EB%A6%AC%EB%B7%B0_Precise%20Zero-Shot%20Dense%20Retrieval%20without%20Relevance%20Labels.md)
- [리뷰_Knowledge Distillation_A Survey](ML-AI/%EB%85%BC%EB%AC%B8_Knowledge%20Distillation/%EB%A6%AC%EB%B7%B0_Knowledge%20Distillation_A%20Survey.md)
- [[리뷰]Large Language Models are Zero-Shot Reasoners](ML-AI/%EB%85%BC%EB%AC%B8_Large%20Language%20Models%20are%20Zero-Shot%20Reasoners/%5B%EB%A6%AC%EB%B7%B0%5DLarge%20Language%20Models%20are%20Zero-Shot%20Reasoners.md)
- [Large Language Models are Zero-Shot Reasoners](ML-AI/%EB%85%BC%EB%AC%B8_Large%20Language%20Models%20are%20Zero-Shot%20Reasoners/%5B%EB%A6%AC%EB%B7%B0%5DLarge%20Language%20Models%20are%20Zero-Shot%20Reasoners.textbundle/Large%20Language%20Models%20are%20Zero-Shot%20Reasoners.md)
- [MATRIX FACTORIZATION TECHNIQUES FOR RECOMMENDER SYSTEMS(feat. Model based CF )](ML-AI/%EB%85%BC%EB%AC%B8_MATRIX%20FACTORIZATION%20TECHNIQUES%20FOR%20RECOMMENDER%20SYSTEMS%28feat.%20Model%20based%20CF%20%EA%B0%9C%EB%85%90%29.textbundle/MATRIX%20FACTORIZATION%20TECHNIQUES%20FOR%20RECOMMENDER%20SYSTEMS%28feat.%20Model%20based%20CF%20%29.md)
- [리뷰_RAFT- Adapting Language Model to Domain Specific RAG](ML-AI/%EB%85%BC%EB%AC%B8_RAFT/%EB%A6%AC%EB%B7%B0_RAFT-%20Adapting%20Language%20Model%20to%20Domain%20Specific%20RAG.md)
- [리뷰_React](ML-AI/%EB%85%BC%EB%AC%B8_React/%EB%A6%AC%EB%B7%B0_React.md)
- [리뷰_RoleLLM _ Benchmarking, Eliciting, and Enhancing Role-Playing Abilities of Large Language Models](ML-AI/%EB%85%BC%EB%AC%B8_Role_LLM/%EB%A6%AC%EB%B7%B0_RoleLLM%20_%20Benchmarking%2C%20Eliciting%2C%20and%20Enhancing%20Role-Playing%20Abilities%20of%20Large%20Language%20Models.md)
- [Rotary Positional Encoding(RoPE)](ML-AI/%EB%85%BC%EB%AC%B8_Rotary%20Positional%20Encoding/Rotary%20Positional%20Encoding%28RoPE%29.md)
- [SELF-CONSISTENCY IMPROVES CHAIN OF THOUGHT REASONING IN LANGUAGE MODELS](ML-AI/%EB%85%BC%EB%AC%B8_SELF-CONSISTENCY%20IMPROVES%20CHAIN%20OF%20THOUGHT%20REASONING%20IN%20LANGUAGE%20MODELS/SELF-CONSISTENCY%20IMPROVES%20CHAIN%20OF%20THOUGHT%20REASONING%20IN%20LANGUAGE%20MODELS.md)
- [Self-Attention with Relative Position Representations](ML-AI/%EB%85%BC%EB%AC%B8_Self-Attention%20with%20Relative%20Position%20Representations/Self-Attention%20with%20Relative%20Position%20Representations.md)
- [리뷰_STaR{ Bootstrapping Reasoning With Reasoning](ML-AI/%EB%85%BC%EB%AC%B8_StaR/%EB%A6%AC%EB%B7%B0_STaR%7B%20Bootstrapping%20Reasoning%20With%20Reasoning.md)
- [GLU Variants Improve Transformer](ML-AI/%EB%85%BC%EB%AC%B8_SwiGLU%20Activation%20Function/GLU%20Variants%20Improve%20Transformer.md)
- [Swish_A SELF-GATED ACTIVATION FUNCTION](ML-AI/%EB%85%BC%EB%AC%B8_Swish_A%20SELF-GATED%20ACTIVATION%20FUNCTION/Swish_A%20SELF-GATED%20ACTIVATION%20FUNCTION.md)
- [리뷰_Two Tales of Persona in LLMs ASurvey of Role Playing and Personalization](ML-AI/%EB%85%BC%EB%AC%B8_Two%20Tales%20of%20Persona%20in%20LLMs%20ASurvey%20of%20Role%20Playing%20and%20Personalization/%EB%A6%AC%EB%B7%B0_Two%20Tales%20of%20Persona%20in%20LLMs%20ASurvey%20of%20Role%20Playing%20and%20Personalization.md)
