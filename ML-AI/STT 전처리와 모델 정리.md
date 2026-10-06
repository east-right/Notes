음성 데이터를 STT (Speech-to-Text) 모델에 입력하기 전 필요한 전처리 과정에 대해 정리하겠습니다. 이 작업에는 노이즈 제거, 볼륨 정규화, 특성 추출과 같은 핵심 단계들이 포함됩니다. 또한, 특정 STT 모델 (예: Whisper, DeepSpeech, Wav2Vec 등)에 맞춘 전처리 방법도 함께 설명하겠습니다.

각 전처리 단계의 중요성과 적용 방법을 설명하고, 사용 가능한 도구 및 라이브러리를 소개하겠습니다. 연구가 완료되면 결과를 공유해드리겠습니다.

Research completed in 7m· 출처 25개

# 음성 데이터 STT 모델 전처리 과정

음성 데이터를 자동 음성 인식(STT) 모델에 투입하기 전에 적절한 **전처리**를 거치면 인식 성능을 향상시킬 수 있습니다. 주로 **노이즈 제거**, **볼륨 정규화**, **음향 특성 추출** 등의 단계가 이루어지며, 사용하는 STT 모델(예: Whisper, DeepSpeech, Wav2Vec 2.0 등)에 따라 최적의 전처리 방식과 요구 조건이 조금씩 다를 수 있습니다. 아래에서는 각 전처리 단계와 모델별 권장 입력 형식 및 기법을 설명합니다.

## 노이즈 제거 (Noise Removal)

환경 잡음이나 배경 소음은 음성 인식의 정확도를 저하시킬 수 있으므로, 음성 데이터를 모델에 넣기 전에 최대한 제거하거나 감소시키는 것이 중요합니다​

[coolpo.io](https://www.coolpo.io/post/how-to-improve-your-audio-quality-with-denoising?e0dbe245_page=2#:~:text=background%20noise%20may%20be%20so,hear%20what%20they%20are%20saying)

. 노이즈 제거를 위해 사용할 수 있는 기법들은 다음과 같습니다:

- **필터링 기법**: 불필요한 주파수 대역의 소음을 필터로 제거합니다. 예를 들어 음성 주파수 범위(일반적으로 300Hz ~ 3400Hz)에 맞춰 **하이패스 필터**를 적용하면 그 이하의 저역 불필요한 소음을 줄일 수 있고, **로우패스 필터**로 고주파 잡음을 걸러낼 수 있습니다. 또한, 전원 hum_noise(50Hz 또는 60Hz)와 같은 특정 주파수의 잡음은 **노치 필터**로 제거할 수 있습니다. 이러한 필터링은 Python의 **SciPy** 라이브러리(`scipy.signal`)로 Butterworth 필터 등을 설계하여 적용할 수 있습니다​
    
    [emerginginvestigators.org](https://emerginginvestigators.org/articles/22-262/pdf#:~:text=,More)
    
    .
    
- **스펙트럴 서브트랙션 및 위너 필터**: 녹음된 신호에서 **노이즈의 주파수 성분**을 추정하여 원 신호에서 빼주는 방식입니다. 일정한 배경 **험(hum)**이나 **화이트 노이즈** 등 **정적(noise stationary)** 잡음의 경우, 해당 잡음의 평균 스펙트럼을 추정한 후 음성 신호의 스펙트럼에서 이를 차감(spectral subtraction)하면 잡음을 감소시킬 수 있습니다. **위너 필터(Wiener filtering)**나 **칼만 필터(Kalman filtering)**도 자주 쓰이는 고전적인 잡음 제거 알고리즘으로, 배경 소음의 통계적 특성을 이용해 신호 대 잡음비(SNR)를 개선합니다​
    
    [coolpo.io](https://www.coolpo.io/post/how-to-improve-your-audio-quality-with-denoising?e0dbe245_page=2#:~:text=For%20example%2C%20if%20the%20background,Wiener%20filtering%2C%20and%20Kalman%20filtering)
    
    . 이러한 기법들은 일반적으로 주파수 도메인에서 동작하며, Python에서는 **NumPy**와 **SciPy**로 푸리에 변환 후 연산하거나, **librosa**를 사용하여 손쉽게 구현할 수 있습니다.
    
- **노이즈 프로파일 기반 제거**: **Audacity**와 같은 오디오 편집 소프트웨어에서 제공되는 방법으로, **대표적인 잡음 구간**을 미리 선택하여 잡음 프로파일을 추출한 뒤 이를 이용해 전체 신호에서 해당 잡음을 제거하는 방식입니다. Python 구현으로는 **noisereduce**와 같은 패키지가 이러한 **스펙트럴 게이팅(spectral gating)** 기반 알고리즘을 제공합니다​
    
    [pypi.org](https://pypi.org/project/noisereduce/1.0.1/#:~:text=,noise%20intended%20to%20be%20removed)
    
    . 이 알고리즘은 Audacity의 노이즈 제거 효과를 기반으로 하며, **잡음만 포함된 짧은 클립**과 **잡음이 섞인 신호 클립**을 입력으로 받아 FFT 변환 후 잡음의 주파수별 통계를 계산하고, 그에 따라 신호 스펙트럼을 마스킹하여 잡음을 제거합니다​
    
    [pypi.org](https://pypi.org/project/noisereduce/1.0.1/#:~:text=1,noise%20intended%20to%20be%20removed)
    
    .
    
- **노이즈 게이트(Noise Gate)**: 일정 임계치 이하의 소리는 **무시**하고 차단하는 방식입니다. 주변 소음이 비교적 일정한 크기로 존재할 경우, 오디오 신호의 **진폭이 설정한 임계값 밑으로 떨어질 때 해당 구간을 0으로 처리**함으로써 미약한 배경음을 제거할 수 있습니다​
    
    [coolpo.io](https://www.coolpo.io/post/how-to-improve-your-audio-quality-with-denoising?e0dbe245_page=2#:~:text=Another%20approach%20to%20audio%20denoising,still%20capturing%20the%20speaker%27s%20voice)
    
    . 이는 특히 에어컨 hum과 같이 연속적인 배경음에 유용하며, **pydub** 라이브러리의 `AudioSegment`에서 제공되는 `split_to_mono`나 **음압(dBFS)** 기반의 `threshold` 설정으로 간단히 구현할 수 있습니다.
    
- **음성 구간 검출 및 무음 제거**: 녹음 내내 말소리가 없는 구간 (무음이나 소음만 있는 부분)을 제거하면 모델이 불필요한 부분을 처리하지 않아도 됩니다. **음성 활동 검출(VAD, Voice Activity Detection)**을 통해 말이 있는 구간만 추출할 수 있습니다. 단순한 방법으로는 **Zero-Crossing Rate(ZCR)**나 프레임 에너지 등을 이용해 구현할 수 있는데, 예를 들어 ZCR은 신호가 0을 기준으로 부호가 바뀌는 빈도로 정의되며​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Zero-crossing_rate#:~:text=The%20zero,2)
    
    , 이는 신호의 거칠기나 잡음 수준을 나타냅니다. ZCR이 낮으면 비교적 **안정적인(발성) 구간**, 높으면 **잡음이나 무성음 구간**일 가능성이 높으므로 이를 이용해 음성 여부를 판단할 수 있습니다​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Zero-crossing_rate#:~:text=For%20monophonic%20%20tonal%20signals%2C,an%20audio%20segment%20or%20not)
    
    . Python의 **librosa.feature.zero_crossing_rate** 함수를 이용하면 프레임별 ZCR을 쉽게 구할 수 있습니다.
    

이러한 노이즈 제거 전처리는 **librosa**, **pydub**, **noisereduce**, **pyAudioAnalysis** 등의 파이썬 도구를 활용하여 수행할 수 있습니다. 예를 들어 librosa로 오디오를 불러온 후 필터를 적용하거나, noisereduce로 배경소음을 제거한 뒤, pydub으로 무음 구간을 잘라내는 파이프라인을 구축할 수 있습니다. 실제 현업에서는 잡음이 심한 데이터의 경우 **전처리된 데이터로 별도 학습을 진행**하거나, **딥러닝 기반 잡음 제거 모델(예: RNNoise)**을 프런트엔드에 적용해 잡음 제거 후 STT를 수행하기도 합니다.

## 볼륨 정규화 (Volume Normalization)

녹음된 음성 데이터는 녹음 환경이나 장치에 따라 음량 차이가 클 수 있습니다. **볼륨 정규화**는 음성 신호의 **전체적인 음량을 일정한 수준으로 맞추는 작업**으로, 이를 통해 너무 작은 음성은 키우고 너무 큰 신호는 줄여서 모델 입력의 편차를 줄일 수 있습니다​

[fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=Audio%20optimizing%20is%20a%20key,avoid%20digital%20clipping%20or%20distortion)

. 정규화는 모델이 **음량 차이와 무관하게** 일관된 성능을 내도록 도와주며, 클리핑(clipping)을 방지하여 **과도한 입력으로 인한 왜곡**을 피할 수 있습니다.

정규화에는 **피크(peak) 정규화**와 **라우드니스(loudness) 정규화** 두 가지 접근이 있습니다​

[fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=,a%20more%20consistent%20listening%20experience)

:

- **피크 정규화**: 오디오 신호에서 **최대 진폭(peak)**을 검사하여, 그 피크가 목표로 하는 레벨에 도달하도록 **신호 전체를 비례 축소/확대**합니다​
    
    [fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=,a%20more%20consistent%20listening%20experience)
    
    . 예를 들어 디지털 오디오에서 허용되는 최대 값인 0 dBFS(풀스케일 기준 데시벨)에 가장 큰 피크가 맞도록 스케일링하면, 파형의 최대치가 0 dBFS에 근접하게 되고 신호가 최대 동적 범위를 활용하게 됩니다. 이 방식은 구현이 간단하고 **신호의 동적 범위**(작은 소리와 큰 소리 사이의 비율)를 보존하지만, **사람이 느끼는 평균 음량**이 반드시 일관되지는 않을 수 있습니다. Python의 **pydub**에서는 `effects.normalize()` 함수를 사용하여 손쉽게 피크 정규화를 수행할 수 있습니다​
    
    [stackoverflow.com](https://stackoverflow.com/questions/42492246/how-to-normalize-the-volume-of-an-audio-file-in-python#:~:text=from%20pydub%20import%20AudioSegment%2C%20effects)
    
    .
    
- **라우드니스 정규화**: 사람의 **청감 특성**을 고려하여 **평균적인 소리의 크기**(루프 또는 RMS 기반)를 목표 레벨에 맞춥니다​
    
    [fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=,a%20more%20consistent%20listening%20experience)
    
    . 예를 들어 방송 음향에서는 ITU-R BS.1770 표준 등에 따른 **LUFS (Loudness Units Full Scale)** 단위로 -23 LUFS 등에 맞추는 방식이 쓰입니다. 구현적으로는 신호의 **평균 제곱근 음압(RMS)**이나 **등가음량**을 계산한 후, 대상 신호의 dBFS를 목표치 (예: -20 dBFS 등)로 맞추도록 이득을 조정합니다​
    
    [stackoverflow.com](https://stackoverflow.com/questions/42492246/how-to-normalize-the-volume-of-an-audio-file-in-python#:~:text=If%20you%20want%20an%20audio,and%20adjust%20as%20needed)
    
    . 이 접근법은 트랙 간의 **체감 음량**을 일정하게 만들어 주며, 사용자에게 일관된 볼륨으로 들리게 합니다.
    

또한 **0 dBFS**는 디지털 오디오에서 최대 진폭을 의미하며, 이를 초과하면 클리핑이 발생해 왜곡된 소리가 납니다. 따라서 정규화 과정에서는 **최대치가 0 dBFS를 넘지 않도록** 약간 여유를 두는 것이 일반적입니다​

[fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=Understanding%20decibels%20)

(예를 들어 -1 dBFS로 피크를 제한). **librosa.util.normalize** 함수를 사용하면 배열 기반의 오디오 신호를 절대 최대치 1.0 (0 dBFS에 대응)에 맞춰 쉽게 정규화할 수 있습니다. 또는 pydub의 `audio_segment.dBFS` 속성과 `apply_gain()` 메서드를 사용하여 특정 목표 dB로 맞추는 것도 가능합니다​

[stackoverflow.com](https://stackoverflow.com/questions/42492246/how-to-normalize-the-volume-of-an-audio-file-in-python#:~:text=If%20you%20want%20an%20audio,and%20adjust%20as%20needed)

.

실무적으로, 음성 인식 데이터를 다룰 때는 **모든 오디오 클립의 평균 음량을 일정 수준으로 맞춰두면** 모델이 볼륨 차이에 덜 민감해집니다. 특히 여러 출처에서 수집된 음성 데이터를 학습시키는 경우, 정규화를 통해 데이터 간 편차를 줄이는 것이 효과적입니다. 단, 너무 강한 정규화나 **과도한 압축**은 자연스러운 말소리의 **억양이나 강세 정보를 잃게 할 수 있으므로**, STT 모델 입력용 정규화는 **과하지 않게** 하는 것이 좋습니다.

## 특성 추출 (Feature Extraction)

원시 오디오 파형은 시간에 따른 진폭 정보를 담고 있지만, 이를 그대로 모델에 넣기보다는 음향학적으로 중요한 **특징(feature)** 들을 추출하여 입력으로 사용하는 경우가 많습니다​

[analyticsvidhya.com](https://www.analyticsvidhya.com/blog/2021/06/mfcc-technique-for-speech-recognition/#:~:text=Speech%20Recognition%20is%20a%20supervised,features%20from%20the%20audio%20signal)

. 특징 추출을 통해 **불필요한 정보나 잡음은 줄이고**, 음성의 **주요 주파수 패턴이나 구조를 부각**시킬 수 있습니다. 대표적인 음향 특징으로 **MFCC**, **멜-스펙트로그램**, **Chroma(크로마)**, **Zero-Crossing Rate** 등이 있습니다:

- **MFCC** (Mel-Frequency Cepstral Coefficients, 멜 주파수 켑스트럼 계수): 음성 인식 분야에서 가장 전통적으로 널리 쓰이는 특성입니다​
    
    [analyticsvidhya.com](https://www.analyticsvidhya.com/blog/2021/06/mfcc-technique-for-speech-recognition/#:~:text=Speech%20Recognition%20is%20a%20supervised,features%20from%20the%20audio%20signal)
    
    . MFCC는 **소리의 짧은 구간 프레임마다 주파수 스펙트럼의 형태**를 요약하여 줍니다. 구체적으로, **프레임별로 FFT를 구해 스펙트럼을 얻고**, 이를 **멜(Mel) 스케일**로 필터링하여 인간 청각의 주파수 인지 특성을 반영한 후, **로그 스펙트럼**에 대해 **DCT(Discrete Cosine Transform)**를 적용하여 얻은 계수들이 MFCC입니다​
    
    [analyticsvidhya.com](https://www.analyticsvidhya.com/blog/2021/06/mfcc-technique-for-speech-recognition/#:~:text=In%20simpler%20terms%2C%20MFCCs%20are,scaled%20spectrum)
    
    . 일반적으로 12~13차의 MFCC 계수에 0차 에너지 계수를 추가하고, 각각의 **델타(1차 미분)**와 **델타-델타(2차 미분)**를 포함하여 총 39차원의 특징 벡터를 사용합니다. MFCC는 **사람 음성의 포먼트(formant)** 등의 중요한 **스펙트럼 형상**을 잘 포착하며, 불필요한 상세 주파수 성분은 줄여주는 효과가 있습니다​
    
    [analyticsvidhya.com](https://www.analyticsvidhya.com/blog/2021/06/mfcc-technique-for-speech-recognition/#:~:text=computed%20from%20the%20mel)
    
    . 이러한 이유로 잡음에 어느 정도 robust하면서도 음색과 발음의 차이를 모델이 학습하기 쉽게 해주어, 과거부터 HMM-GMM 기반 음성 인식부터 딥러닝 기반까지 폭넓게 사용되어 왔습니다. Python에서는 **librosa.feature.mfcc()** 함수를 통해 손쉽게 MFCC를 계산할 수 있습니다. MFCC를 시각화하면 시간-프레임 축을 따라 Cepstral 계수가 변화하는 2D **스펙트로그램 유사 이미지**가 나오며, 이는 모델 입력 또는 특징 분석에 활용됩니다.
    
- **멜-스펙트로그램**: 멜-스펙트로그램은 **일반 스펙트로그램을 멜 주파수 축으로 변환**한 것입니다. 스펙트로그램은 시간 대비 주파수 성분의 강도를 나타낸 것으로, 멜-스펙트로그램은 여기에 **멜 스케일 필터뱅크**를 적용하여 주파수 축을 **로그 스케일(멜 척도)**로 변환한 것입니다​
    
    [huggingface.co](https://huggingface.co/learn/audio-course/en/chapter1/audio_data#:~:text=To%20create%20a%20mel%20spectrogram%2C,frequencies%20to%20the%20mel%20scale)
    
    . 이를 통해 인간의 청각에 가까운 해상도로 주파수 정보를 볼 수 있으며, 저주파수대는 세밀하게, 고주파수대는 덜 세밀하게 표현됩니다. 멜-스펙트로그램은 추가로 **로그 스케일 진폭(dB)**로 변환하여 **로그-멜 스펙트로그램** 형태로도 많이 사용합니다. 이는 **음성 인식 및 음성 처리 모델에서 가장 많이 사용하는 입력 표현** 중 하나로​
    
    [huggingface.co](https://huggingface.co/learn/audio-course/en/chapter1/audio_data#:~:text=match%20at%20L430%20Compared%20to,identification%2C%20and%20music%20genre%20classification)
    
    , 원시 파형보다 의미 있는 주파수 특징을 제공하면서도 MFCC처럼 DCT 변환을 하지 않아 정보를 완전히 축약하지는 않으므로, **딥러닝 기반 모델의 입력**으로 특히 선호됩니다. 예를 들어 **OpenAI Whisper**와 같은 모델은 입력으로 80차원 **로그-멜 스펙트로그램**을 사용합니다​
    
    [cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)
    
    . librosa의 **melspectrogram()** 함수와 **power_to_db()** 함수를 조합하여 멜-스펙트로그램을 계산 및 dB 단위로 변환할 수 있습니다. 멜-스펙트로그램은 **시각화가 용이**하여 음성 데이터의 특성을 분석하거나, 데이터 증강(예: 스펙트로그램에 노이즈 추가 등)에도 활용됩니다.
    
- **Chroma(크로마) 특징**: 크로마 특징은 음악 정보 처리 분야에서 나온 개념으로, **12개의 반음 음계(pitch class)**별 **에너지 분포**를 나타낸 것입니다​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Chroma_feature#:~:text=twelve%20different%20pitch%20classes%20,changes%20in%20timbre%20and%20instrumentation)
    
    . 즉, 옥타브를 무시하고 C, C#, D, ... B로 대표되는 12개 음높이 클래스에 신호가 얼마나 에너지를 갖는지 표현하므로, **화성적 구조**나 **선율**을 분석하는데 유용합니다​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Chroma_feature#:~:text=main%20property%20of%20chroma%20features,changes%20in%20timbre%20and%20instrumentation)
    
    . 음성 신호 자체는 음악처럼 뚜렷한 조성을 갖지 않으므로 STT에서 직접 사용되지는 않지만, **화자인식**이나 **감정분석**에서 억양이나 음도의 패턴을 분석하기 위해 응용되기도 합니다. librosa에서는 **librosa.feature.chroma_stft()** 등의 함수로 크로마그램을 구할 수 있습니다.
    
- **Zero-Crossing Rate(ZCR)**: **신호가 0을 몇 번 교차하는지**, 즉 양에서 음 또는 음에서 양으로 부호가 바뀌는 **횟수의 비율**을 나타내는 특징입니다​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Zero-crossing_rate#:~:text=The%20zero,2)
    
    . ZCR은 신호의 **거칠기 또는 주기성**을 반영하는데, 값이 높으면 진동이 빈번하므로 **고주파 성분이나 잡음**이 많음을 시사하고, 값이 낮으면 **주기적인 신호**(예: 낮은 주파수의 성음(有聲音) 등)를 의미합니다. 특히, ZCR은 **음성/무음 구간 검출(VAD)**에 활용되는데, 사람 음성이 있는 구간은 일반적으로 ZCR이 낮고, 무성음이나 잡음 구간은 ZCR이 높으므로 이를 임계치 기반으로 구분할 수 있습니다​
    
    [en.wikipedia.org](https://en.wikipedia.org/wiki/Zero-crossing_rate#:~:text=For%20monophonic%20%20tonal%20signals%2C,an%20audio%20segment%20or%20not)
    
    . 또한 ZCR은 음악 신호에서 **타악기 소리 식별** 등에도 쓰이는 등 폭넓게 활용되는 기본 특징입니다. librosa의 **zero_crossing_rate()**로 프레임 단위 ZCR을 계산할 수 있으며, 신호의 거칠기를 빠르게 파악할 때 유용합니다.
    

이 외에도 **스펙트럼 센트로이드(spectral centroid)**, **스펙트럼 플럭츄에이션(flux)**, **폴리포닉 라이브러리 특징** 등 다양한 음향 특징들이 존재하지만, 음성 인식에서는 대체로 위의 특징들이 많이 사용되거나, 아니면 **딥러닝 모델이 직접 원시 파형으로부터 특징을 학습**하도록 설계됩니다. 특징 추출한 값들은 모델의 입력 벡터로 사용되거나, 데이터 분석 단계에서 활용됩니다. Python에서는 **librosa**를 통해 대부분의 특징을 쉽게 추출할 수 있고, **pyAudioAnalysis** 라이브러리도 여러 특징을 한꺼번에 계산하는 도구를 제공합니다.

## STT 모델별 권장 전처리

각 음성 인식 모델마다 입력으로 요구하는 형식이나 전처리 방식이 조금씩 다릅니다. 아래에서는 Whisper, DeepSpeech, Wav2Vec 2.0 세 가지 모델을 예로 들어, 각각에 맞는 전처리 방법과 요구 사항을 설명합니다.

### OpenAI Whisper 모델

OpenAI의 Whisper 모델은 **엔드-투-엔드 Transformer 기반**의 대규모 음성 인식 모델로, **16 kHz 샘플링된 모노 음성**을 입력으로 받도록 설계되었습니다​

[cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)

. Whisper 모델에 맞추기 위한 전처리 방법은 다음과 같습니다.

- **샘플링 레이트와 포맷**: Whisper는 학습 시 모든 데이터를 **16,000 Hz로 리샘플링**했으며, 최대 **30초 길이의 음성 세그먼트**를 사용했습니다​
    
    [cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)
    
    . 따라서 입력 오디오는 **16 kHz, 모노**로 준비해야 합니다. 일반적인 마이크 녹음이 44.1kHz나 48kHz인 경우, Python에서 **librosa.resample** 또는 **pydub**/ffmpeg 등을 이용하여 16kHz로 다운샘플링합니다. 또한 스테레오인 경우 **모노(Mono)**로 합치거나 한 채널만 사용합니다.
    
- **특징 표현**: Whisper는 모델 입력으로 **로그-멜 스펙트로그램**을 사용합니다. 구체적으로, 25ms 창 및 10ms 홉으로 계산한 **80차원 멜-스펙트로그램**을 로그 스케일 (dB)로 변환한 값을 사용합니다​
    
    [cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)
    
    . Whisper 라이브러리(또는 API)를 통해 예측을 할 때는 이 스펙트로그램 계산이 내부적으로 수행되지만, 만약 직접 전처리를 해야 한다면 **librosa.feature.melspectrogram**과 **power_to_db**를 활용하여 동일한 사양으로 멜-스펙트로그램을 계산해야 합니다. Whisper 논문에 따르면 입력 오디오 파형은 -11 사이로 **정규화 및 평균 제거**가 되어 있고, 그 후 멜 필터뱅크를 적용한다고 명시되어 있습니다​
    
    [cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=25,where%20the%20second)
    
    . 즉, 전처리 단계에서 오디오를 **float형식으로 스케일링(-11 범위)**하고 **DC 성분을 제거(전체 평균 0화)**하면 모델이 기대하는 입력 범위에 맞출 수 있습니다.
    
- **기타 전처리**: Whisper는 비교적 강인하게 훈련되었지만, 여전히 **잡음이 적은 깨끗한 음성**에서 최고의 성능을 발휘합니다. 따라서 앞서 언급한 **노이즈 제거** 전처리를 거친 후 입력하면 오인식률을 낮출 수 있습니다. 특히 Whisper는 30초가 넘는 긴 입력의 경우 자동으로 잘라서 처리하므로, **긴 녹음은 30초 이하로 분할**하는 것이 필요합니다. Whisper 모델은 내부적으로 음성 여부 판별까지 학습되어 있어 **무음 구간도 자체 처리**가 가능하지만, 불필요한 무음은 제거하여 보내는 편이 속도 면에서 유리합니다.
    

### Mozilla DeepSpeech 모델

Mozilla의 DeepSpeech는 Baidu의 DeepSpeech 연구에 기반한 오픈 소스 **RNN 기반** 음성 인식 엔진입니다. 이 모델은 입력으로 **PCM 음성波形 데이터를 직접 수신**하며, 내부에서 해당 신호의 **MFCC 특징을 추출**하여 인식에 활용합니다​

[discourse.mozilla.org](https://discourse.mozilla.org/t/audio-file-specifications-to-use-deep-speech/63716#:~:text=lissyx%20%28%28slow%20to%20reply%29%20,14%2C%202020%2C%205%3A11pm%20%208)

. DeepSpeech를 위한 전처리 유의사항은 다음과 같습니다.

- **샘플링 레이트와 형식**: Mozilla DeepSpeech의 사전 학습된 모델은 **16-bit, 16 kHz, 모노 WAV** 형식의 데이터를 사용합니다​
    
    [discourse.mozilla.org](https://discourse.mozilla.org/t/audio-file-specifications-to-use-deep-speech/63716#:~:text=lissyx%20%28%28slow%20to%20reply%29%20,14%2C%202020%2C%205%3A11pm%20%208)
    
    . 따라서 준비한 음성 파일이 MP3이나 다른 형식일 경우, 먼저 **WAV (PCM) 포맷**으로 변환하고 **16 kHz로 샘플링 레이트 변환**을 해주는 것이 좋습니다. 이는 훈련 데이터 사양과 일치시키기 위함이며, DeepSpeech 엔진은 추론 시 입력된 신호를 내부적으로 16kHz로 가정하고 처리합니다. (DeepSpeech 개발진에 따르면 8kHz 모델도 별도로 존재하지만, 일관성을 위해 훈련과 동일한 조건의 입력이 권장됩니다​
    
    [discourse.mozilla.org](https://discourse.mozilla.org/t/audio-file-specifications-to-use-deep-speech/63716#:~:text=lissyx%20%28%28slow%20to%20reply%29%20,14%2C%202020%2C%205%3A11pm%20%208)
    
    .)
    
- **음성 길이 및 세그먼트화**: 아주 긴 오디오를 한번에 인식하는 경우 메모리 및 정확도 이슈가 있을 수 있습니다. Mozilla에서도 한 WAV 파일당 **약 10~15초 이하** 길이를 권장하고 있으므로​
    
    [discourse.mozilla.org](https://discourse.mozilla.org/t/audio-file-specifications-to-use-deep-speech/63716#:~:text=file%20size%20%3F)
    
    , 긴 녹음은 문장 단위 등으로 **분할하여 처리**하는 것이 좋습니다. 이를 위해 자동 음성 분할기나 VAD를 사용해 미리 클립을 나누어 둘 수 있습니다.
    
- **노이즈 및 볼륨 전처리**: DeepSpeech 모델은 내부적으로 잡음에 대한 견고성을 어느 정도 갖추고 있지만, **현실 세계 잡음**에 대해서는 여전히 전처리를 해주는 편이 인식률 향상에 도움이 됩니다. 따라서 **노이즈 제거** 필터를 통해 배경음을 줄이고, **정규화**를 통해 음량을 적절히 맞춘 후 입력하십시오. 특히 DeepSpeech는 입력 신호의 **절대 진폭**보다는 **패턴**에 반응하므로, 여러 파일의 음량이 들쑥날쑥하다면 정규화를 통해 어느 정도 균일화하는 것이 좋습니다.
    
- **특징 추출 관련**: 앞서 언급했듯이 DeepSpeech는 **실시간으로 MFCC를 추출**하여 사용하므로, 사용자가 별도로 MFCC를 계산해서 넣을 필요는 없습니다. 다만 사용자 측에서 MFCC를 미리 추출해 모델을 **재학습(training)**시키는 고급 활용도 가능하며, 이 경우 **Cepstral Mean Normalization(CMN)** 등 추가적인 특징 정규화를 수행하기도 합니다. 기본적으로는 원시 파형만 준비하면 모델이 알아서 처리하지만, **샘플링 레이트와 채널 수, 포맷을 맞추는 전처리**가 필수적입니다.
    

### Facebook Wav2Vec 2.0 모델

Facebook AI의 Wav2Vec 2.0은 **Transformer 기반**의 자기지도(Self-Supervised) 학습으로 훈련된 최첨단 음성 인식 모델입니다. **원시 파형(waveform)**을 직접 입력으로 받아 특징을 학습하며, 후단에 CTC 등을 활용해 텍스트를 출력합니다. Wav2Vec 2.0 모델 적용 시 전처리 사항은 아래와 같습니다.

- **샘플링 레이트**: Wav2Vec 2.0은 **LibriSpeech(16kHz) 코퍼스** 등으로 학습되었기에, 입력 음성도 **16 kHz 샘플링**이 요구됩니다​
    
    [kdnuggets.com](https://www.kdnuggets.com/2021/03/speech-text-wav2vec.html#:~:text=Please%20note%20the%20Wav2Vec%20model,%E2%80%98taken%E2%80%99%20audio%20clip%20into%2016kHz)
    
    . 사전 학습된 Wav2Vec2 모델 (예: `facebook/wav2vec2-base-960h`)은 16kHz mono 오디오에 최적화되어 있으므로, 다른 레이트의 데이터는 **resample**을 통해 16000 Hz로 변환해야 합니다. `librosa.load(path, sr=16000)`을 쓰면 불러오며 자동 리샘플링할 수 있고, 또는 **torchaudio** 등의 load 함수도 sr 파라미터를 제공하므로 활용합니다.
    
- **데이터 타입과 정규화**: Wav2Vec2 모델에 입력으로 넣는 파형은 **float32 타입**의 numpy 배열 또는 Tensor로서, 값의 범위는 일반적으로 -1.0 ~ 1.0로 **정규화**되어 있습니다. 특히 연구 논문에서는 **입력 파형을 평균 0, 분산 1로 표준화(zero-mean, unit-variance)**했다고 언급되어 있습니다​
    
    mohitmayank.com
    
    . 따라서 최적 결과를 위해서는, 각 오디오 클립의 DC 오프셋을 제거하고 에너지를 정규화해서 모델에 넣는 것이 권장됩니다. Hugging Face의 `Wav2Vec2FeatureExtractor` (또는 `Wav2Vec2Processor`)를 사용하면 이러한 정규화가 자동으로 적용됩니다.
    
- **무음 및 잡음 처리**: Wav2Vec 2.0은 방대한 데이터로 사전학습되어 잡음에 비교적 강인하지만, 여전히 깨끗한 음성에서 성능이 더 좋습니다. 필요에 따라 **노이즈 억제**를 전처리로 적용하고, 앞뒤의 **장음(무음)** 부분을 잘라내는 것이 도움이 될 수 있습니다. 특히 Wav2Vec2는 **자기지도학습**으로 음성 특징을 학습했기 때문에, 입력에 극단적으로 다른 소리(예: 아주 큰 잡음 펄스 등)가 섞이면 특징 추출에 혼란을 줄 수 있으므로 이를 피하는 편이 좋습니다. 한편 ZCR 기반 VAD로 무음을 제거하거나, 일정 시간 이상 무음일 때 잘라주는 등의 처리를 통해 **실시간 응답 지연**도 줄일 수 있습니다.
    
- **특징 추출**: Wav2Vec 2.0은 별도의 수작업 특징 (MFCC 등)을 사용하지 않고 **모델 내부의 CNN Encoder가 파형으로부터 특징을 추출**합니다. 따라서 사용자는 **원시 오디오**만 올바르게 입력하면 되며, 앞서 말한 샘플링 레이트 및 정규화만 맞춰주면 됩니다. 만약 사용자 정의로 Wav2Vec2를 학습시킬 경우, 라이브러리에서 제공하는 `Wav2Vec2FeatureExtractor`를 통해 동일한 방법으로 전처리를 해야 합니다. 이 때 time-stretch나 pitch-shift 같은 **데이터 증강**을 추가로 적용해도 모델 입력은 여전히 raw waveform이므로, 증강 후에도 16kHz 기준으로 맞춰주는 것을 잊지 않아야 합니다.
    

以上が 일반적인 STT 전처리 과정 및 모델별 권장사항입니다. 실제 프로젝트에서는 여기 언급된 전처리 기법들을 조합하여 파이프라인을 구성하고, 데이터에 맞게 파라미터를 튜닝해야 최상의 성능을 얻을 수 있습니다. 요약하면, **깨끗하고 일정한 음질**(노이즈 최소화, 볼륨 일정)로 **모델이 요구하는 형식**(샘플링레이트, 채널, 시간길이 등)에 맞춰 데이터를 준비하는 것이 핵심입니다. 이러한 전처리 단계를 통해 STT 모델이 음성의 내용에 집중할 수 있게 하고 잡음 요인을 줄여, **더 정확하고 신뢰할 수 있는 음성 인식 결과**를 얻을 수 있을 것입니다.

**참고 문헌 및 자료:** 자동 음성 인식 전처리에 대한 일반적인 권장사항은 다양한 소스에서 확인할 수 있습니다​

[coolpo.io](https://www.coolpo.io/post/how-to-improve-your-audio-quality-with-denoising?e0dbe245_page=2#:~:text=background%20noise%20may%20be%20so,hear%20what%20they%20are%20saying)

​

[coolpo.io](https://www.coolpo.io/post/how-to-improve-your-audio-quality-with-denoising?e0dbe245_page=2#:~:text=For%20example%2C%20if%20the%20background,Wiener%20filtering%2C%20and%20Kalman%20filtering)

​

[fastpix.io](https://www.fastpix.io/blog/optimizing-the-loudness-of-audio-content#:~:text=,a%20more%20consistent%20listening%20experience)

​

[cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)

. 특히 Whisper 논문​

[cdn.openai.com](https://cdn.openai.com/papers/whisper.pdf#:~:text=audio%20is%20re,where%20the%20second)

, DeepSpeech FAQ​

[discourse.mozilla.org](https://discourse.mozilla.org/t/audio-file-specifications-to-use-deep-speech/63716#:~:text=lissyx%20%28%28slow%20to%20reply%29%20,14%2C%202020%2C%205%3A11pm%20%208)

, Wav2Vec 2.0 자료​

mohitmayank.com

에서 각 모델의 입력 요구사항과 전처리 내용을 확인할 수 있습니다. Python 라이브러리 사용 예시는 librosa와 pydub의 공식 문서 및 예제 코드​

[stackoverflow.com](https://stackoverflow.com/questions/42492246/how-to-normalize-the-volume-of-an-audio-file-in-python#:~:text=If%20you%20want%20an%20audio,and%20adjust%20as%20needed)

, 그리고 OpenAI Whisper와 HuggingFace Transformers의 가이드에서 찾아볼 수 있습니다. 부디 이러한 정보를 활용하여 효과적인 전처리 파이프라인을 구축하시기 바랍니다.