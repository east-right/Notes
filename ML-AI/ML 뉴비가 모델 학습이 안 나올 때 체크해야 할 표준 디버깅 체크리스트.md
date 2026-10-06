ML 뉴비가 모델 학습이 안 나올 때 체크해야 할 표준 디버깅 체크리스트입니다. 보통 모델 구조 문제보다는 데이터나 학습 파이프라인에서 문제가 터지는 경우가 90% 이상입니다.

1. 데이터 및 파이프라인 검증 (가장 중요)
- Overfit on a single batch: 아주 작은 데이터(예: 1~2개 바치)만 넣고 Loss가 0으로 오버피팅되는지 확인하세요. 이게 안 되면 모델 구현이나 Loss 계산, 백프로파게이션 로직 자체에 버그가 있는 겁니다.
- 데이터 전처리/파이프라인 확인: 입력 데이터의 Normalized 값, 레인지, 채널 순서(RGB/BGR), 레이블 인코딩이 정상인지 직접 프린트해서 visual check를 하세요.
- Data Leakage / Target Shift: Train/Val 데이터 분리가 제대로 되었는지, 평가 메트릭 계산 시 레이블 토큰이 잘못 들어가는지 확인합니다.

2. 하이퍼파라미터 및 학습 설정
- Learning Rate 스위핑: LR이 너무 크면 폭발하거나 맴돌고, 너무 작으면 아예 안 돕니다. 보통 1e-4, 3e-4, 1e-3 등 로그 스케일로 스위핑해보세요.
- Warmup 및 Scheduler: 초기 Gradient 폭발을 막기 위해 Warmup을 적용했는지, Learning Rate Decay가 너무 일찍 들어가지 않는지 확인합니다.
- Batch Size & Optimizer: AdamW가 가장 무난하며, Batch Size 변화에 따라 LR도 비례해서 조절했는지 체크하세요.

3. 모델 구조 및 그래디언트 흐름
- Gradient Vanishing / Explosion: Gradient Norm을 출력해보고 vanishing이나 exploding이 나는지 확인하세요. Gradient Clipping(예: 1.0)을 적용하는 것이 좋습니다.
- 가중치 초기화(Initialization): Custom 모델의 경우 Xavier/He 초기화가 안 되어 초기 Loss가 이상하게 시작할 수 있습니다.
- Residual Connection / Normalization: LayerNorm/BatchNorm의 위치(Pre-LN vs Post-LN)나 Skip Connection 연결이 누락되지 않았는지 점검하세요.

4. Baseline 모델 비교
- 검증된 SOTA/Standard Baseline 모델(예: ResNet, ViT, Llama 등)을 동일한 데이터셋과 파이프라인으로 돌려보고, 본인이 새로 만든 모델과 성능 차이를 정밀 비교하세요.
(2/3)