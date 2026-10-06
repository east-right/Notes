#DNN #LLM #활성화함수 관련
GLU는 신경망의 학습 과정에서 입력 데이터의 중요도를 결정하는 게이팅(Gating) 메커니즘을 도입하여, 불필요한 정보를 차단하고 중요한 정보만을 다음 레이어로 전달하는 방식으로 작동한다. 특히, GLU는 순환 신경망(RNN)의 gating을 단순화한 형태로, 기울기 소실 문제를 줄이고, 더 빠르게 수렴하면서도 높은 정확도를 달성할 수 있게 한다.

1. Gating의 역할 : gating은 입력 데이터를 필요에 따라 선택적으로 통과시키거나 차단하는 역할을 한다. 이를 통해 모델이 각 입력이 얼마나 중요한지를 결정할 수 있다. 예를들어, 다음 단어를 예측할 때 특정 단어의 중요도가 높다면 게이트가 그 정보를 더 강하게 전달하고, 그렇지 않은 정보는 줄이는 방식이다.
    
2. GLU의 구조 : GLU는 다음과 같이 수식으로 표현된다.

    ![](assets/Pasted%20image%2020250420190313.png)
    
    - X : 입력 벡터
    - W, V : 학습 가능한 가중치
    - b, c : 편향
    - $\sigma$ : 시그모이드 함수로 비선형 활성화 함수(게이트 역할을 함)
    - $\otimes$ : 요소별 곱셈을 의미 -> 합성곱아님#

![](assets/Pasted%20image%2020250420190328.png)

- 비선형성과 선형 경로 제공 : gate가 비선형성을 제공하는 동시에, 선형 경로도 포함하여 기울기 소실 문제를 완화한다. 이는 신호가 여러 층을 통과할 때도 기울기가 너무 작아지지 않도록 도와주며, 깊은 신경망에서도 학습이 효율적으로 진행된다.
- RNN 게이팅과의 차이점 : RNN 에서는 입력 게이트, 출력 게이트, 망각 게이트와 같은 복잡한 구조가 필요하지만, GLU는 단순히 하나의 게이트만을 사용하여 정보를 조절한다. 이렇게 단순화된 구조 덕분에, 계산 효율성이 높아지고 학습이 더 빠르게 진행된다.

이 방식은 **선형성**과 **비선형성**을 결합하여 더 강력한 표현력을 제공하며, 딥러닝 모델의 학습 과정에서 중요한 정보만을 효과적으로 학습할 수 있게 도와준다.

---

reference

- [https://medium.com/@pragyansubedi/gated-linear-unit-enabling-stacked-convolutions-to-out-perform-rnns-ea08daa653b8](https://medium.com/@pragyansubedi/gated-linear-unit-enabling-stacked-convolutions-to-out-perform-rnns-ea08daa653b8)
- [https://velog.io/@euisuk-chung/개념-GLU와-그-변형들-역사와-주요-개념-정리](https://velog.io/@euisuk-chung/%EA%B0%9C%EB%85%90-GLU%EC%99%80-%EA%B7%B8-%EB%B3%80%ED%98%95%EB%93%A4-%EC%97%AD%EC%82%AC%EC%99%80-%EC%A3%BC%EC%9A%94-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)