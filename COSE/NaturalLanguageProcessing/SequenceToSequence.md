# 🔊Sequence-to-Sequence

4/10 수업

---
## Machine Translation
기계 번역

소스 언어 문장 w(s)가 주어졌을 때 가장 좋은 타겟 언어 번역 w^(t)를 찾는 최적화 문제  
<img width="277" height="78" alt="image" src="https://github.com/user-attachments/assets/fbff8012-2f95-4493-9629-df9ad3e52e83" />  
(ψ는 소스-타겟 문장 쌍을 평가하는 스코어링 함수)

### 번역이 어려운 이유
- 단어 ↔ 구 대응: "I like apples" ↔ "J'aime les pommes" (단어 하나가 여러 단어로 대응됨)
- 어순 변화: 영어에서 "red apples"이지만 프랑스어에서는 "pommes rouges" (형용사 위치가 다름)  
- 문맥 의존성: "les"는 단독으로는 "the"이지만, "les pommes"는 "apples"로 번역됨 (문맥에 따라 뜻이 달라짐)

### 번역 평가 기준
1. Adequacy (충실성): 번역문 w(t)이 원문 w(s)의 언어적 내용을 충분히 반영하는가?
2. Fluency (유창성): 번역문 w(t)이 타겟 언어로서 자연스러운 문장인가?

<img width="721" height="210" alt="image" src="https://github.com/user-attachments/assets/0384d207-5e10-4765-9b7c-9a5a2abf5e9a" />

### BLEU 스코어 (자동 평가 지표)
시스템이 생성한 번역(hypothesis)과 참조 번역(reference)의 n-gram 정밀도를 비교

<img width="618" height="258" alt="image" src="https://github.com/user-attachments/assets/e255a9e6-9778-42cb-a78c-92b984418756" />

#### 주의사항
1. log 0 방지: 모든 precision에 스무딩(smoothing)을 적용
2. 중복 사용 방지: reference의 각 n-gram은 최대 한 번만 사용 가능 
    ex) hypothesis가 "to to to to to"이고 reference가 "to be or not to be"일 때, unigram precision이 1이 되어선 안 됨
3. Brevity Penalty (짧은 번역 패널티): precision 기반 지표는 짧은 번역에 유리하므로, 번역이 reference보다 짧을 경우 패널티 부여: BP=e^(1−(r/h)) (h<r 일 때)  
    (r은 reference 길이, h는 hypothesis 길이)

#### 한계
- 좋은 번역이더라도 **참조 번역과 n-gram이 다르면 낮은 점수를 받을 수 있음**  
보완하기 위한 대안으로 METEOR (weighted F-measure)와 TER (Translation Error Rate, 편집 거리 기반)가 제안

### Statistical Machine Translation (SMT)
확률적 모델을 데이터로부터 학습

<img width="508" height="218" alt="image" src="https://github.com/user-attachments/assets/874fd7a2-a42d-4dc7-9a26-54af453b1fed" />

<img width="713" height="160" alt="image" src="https://github.com/user-attachments/assets/f4e67ae7-b793-45a4-ad6f-b0ff1fa15ac1" />

<img width="705" height="190" alt="image" src="https://github.com/user-attachments/assets/efd9984d-bc7e-4a41-948c-cd1e04b5cc87" />

#### 한계
- 각 언어 쌍마다 개별 서브 컴포넌트를 설계해야 함
- 특수 언어 현상을 위한 feature engineering이 필요
- 유지 비용이 매우 큼


## Sequence-to-Sequence (Seq2Seq)

### Neural Machine Translation (NMT)
**단 하나의 end-to-end 신경망**으로 번역 전체를 수행 --> **Sequence-to-Sequence (seq2seq) 모델**  
두 개의 RNN (Encoder + Decoder)로 구성

### Encoder
소스 문장을 읽어 고정 크기의 벡터로 압축하는 RNN

<img width="887" height="482" alt="image" src="https://github.com/user-attachments/assets/0d412470-d6d9-4541-89bf-5d24f6184e91" />

<img width="683" height="200" alt="image" src="https://github.com/user-attachments/assets/cf8fa176-96d9-4cfa-bcfe-370d560aa707" />

**양방향(bidirectional) RNN**을 사용 (양방향을 쓰면 각 위치에서 앞뒤 문맥을 모두 반영할 수 있기 때문)

#### 파라미터
- 소스 언어 워드 임베딩 E(s)
- RNN 파라미터 {W,U,b} (simple RNN 기준), LSTM은 4배
- Encoder는 출력 행렬 없음

### Decoder
h_enc를 초기 hidden state로 받아 타겟 언어 단어를 하나씩 생성하는 RNN  
**조건부 언어 모델(conditional language model)**이라 부릅
- 언어 모델: 다음 단어를 예측하기 때문
- 조건부: 소스 문장 w(s)에 h_enc를 통해 조건화되기 때문

<img width="909" height="437" alt="image" src="https://github.com/user-attachments/assets/8ce88aee-eb47-4536-9867-b502b2b089e5" />

<img width="758" height="310" alt="image" src="https://github.com/user-attachments/assets/e7e8f516-b926-4764-9882-771df5a97d0e" />

**Decoder는 반드시 단방향(left-to-right)**이어야 함 (미래 정보를 미리 참조할 수 없기 때문)

#### 파라미터
- 타겟 언어 워드 임베딩 E(t)
- RNN 파라미터 {W,U,b}
- 출력 행렬 Wo​ (타겟 임베딩 E(t)와 weight tying 가능)

<img width="811" height="283" alt="image" src="https://github.com/user-attachments/assets/9c98bf2e-dde7-4408-b95e-0ea11a788fb6" />

### Seq2Seq 학습
<img width="696" height="159" alt="image" src="https://github.com/user-attachments/assets/0cb30670-e17c-481d-a1f4-8a9c0b9cdc2b" />

- 하나의 시스템으로 end-to-end로 최적화
- 역전파(backpropagation)가 디코더 → 인코더 방향으로 모두 흐르며, 두 RNN의 파라미터가 함께 업데이트

### Greedy Decoding vs Beam Search
Greedy Decoding: 매 timestep마다 가장 높은 확률의 단어만 선택  
<img width="325" height="64" alt="image" src="https://github.com/user-attachments/assets/08ce5681-b5e4-43ad-861f-7ac4ad845929" />  
구현이 간단하지만, 초기 선택이 잘못되면 이후 전체 시퀀스가 나빠질 수 있음

Exhaustive Search (완전 탐색): 이론적으로 최적이지만 계산량이 폭발적  
<img width="275" height="58" alt="image" src="https://github.com/user-attachments/assets/62b6f85e-6e94-4078-9e96-01f8834045d0" />  
T도 모르고, 어휘 크기가 ∣V∣라면 ∣V∣^T개의 가능성을 모두 탐색해야 합

Beam Search (절충안): 매 timestep에서 **k개의 가장 그럴듯한 가설(hypothesis)**을 유지, 모든 k개 가설이 `<eos>`를 생성했거나, 최대 디코딩 길이 T에 도달할 때 종료  
<img width="697" height="226" alt="image" src="https://github.com/user-attachments/assets/8bbe33df-4455-49f9-b374-36a0e2006bd7" />  
<img width="609" height="132" alt="image" src="https://github.com/user-attachments/assets/58918eca-514e-4990-8efa-6a4ebc755b45" />  
정규화를 하는 이유: **짧은 시퀀스의 로그 확률 합이 항상 큼** --> 불공평  
Beam search는 exhaustive search보다 훨씬 효율적이지만, 전역 최적(globally optimal)을 보장하지는 않음

<img width="728" height="299" alt="image" src="https://github.com/user-attachments/assets/daee4908-cf40-43ff-b845-59c7b5762c51" />

### Seq2Seq의 다양한 응용
기계 번역 외에도 입력-출력이 모두 시퀀스인 다양한 NLP 태스크에 적용
- 텍스트 요약: 긴 뉴스 기사 → 짧은 요약문
- 대화 시스템: 이전 발화 시퀀스 → 다음 응답
- 구문 분석 (Parsing): 문장 → 파스 트리 (괄호 표기 시퀀스로 직렬화)  
  예: "John has a dog ." → "(S (NP NNP)_NP (VP VBZ (NP DT NN)_NP)_VP .)_S"
- 코드 생성: 자연어 문제 설명 → Python 코드


## Attention Mechanism

Seq2Seq의 근본적 한계: 모든 정보를 단 하나의 고정 크기 벡터 h_enc에 압축해야 함  
==>
1. 긴 문장을 처리할수록 초반 정보가 손실됨
2. 역전파 시 기울기가 긴 경로를 거쳐 전달되므로 기울기 소실 문제 발생 (Vanishing Gradient)

==> Attention Mechanism: 디코딩의 각 timestep t마다, 디코더의 현재 hidden state h_dec_t​를 기반으로 **소스 문장의 어느 부분에 집중할지를 동적으로 결정**

### Attention 계산 과정
<img width="280" height="59" alt="image" src="https://github.com/user-attachments/assets/539a9c0b-6341-4447-a715-eba666cb3f64" />

#### Step 1: Attention Score 계산
각 인코더 hidden state와 현재 디코더 hidden state의 유사도를 계산  
"현재 디코딩하려는 단어 입장에서, 각 소스 단어가 얼마나 관련 있는가?"를 나타냄  
<img width="495" height="55" alt="image" src="https://github.com/user-attachments/assets/ae3648ec-e837-44f7-9c7b-3e6548b5e6da" />

#### Step 2: Softmax로 Attention Distribution 획득
스코어를 확률 분포로 정규화: 소스 문장의 각 위치에 대한 **집중도(attention weight)**  
<img width="314" height="66" alt="image" src="https://github.com/user-attachments/assets/f1fd2b83-98ac-4335-a672-4d93298f052f" />

#### Step 3: Attention Output (Context Vector) 계산
Attention distribution을 가중치로 삼아 인코더 hidden state들의 가중합을 구함  
<img width="200" height="81" alt="image" src="https://github.com/user-attachments/assets/1cc46a4b-5b58-4598-9b38-79fa1120751c" />  
이 벡터는 현재 디코딩 단계에서 소스 문장의 관련 부분을 요약한 **context vector**

#### Step 4: Decoder 출력에 통합
Context vector a_t​와 디코더 hidden state h_dec_t​를 concatenate한 후 변환  
<img width="543" height="120" alt="image" src="https://github.com/user-attachments/assets/36de1554-37c6-4ee4-b633-dbe2de1babba" />  
이 h~_t​를 출력층에 전달하여 다음 단어를 예측  
<img width="196" height="38" alt="image" src="https://github.com/user-attachments/assets/0351bd39-6a59-4b7e-8fe4-f51894b1d0df" />

### Attention Score 함수의 종류
#### 1. Dot-product Attention (내적)
<img width="247" height="44" alt="image" src="https://github.com/user-attachments/assets/1b45a118-f91f-4008-8514-929b64024fe7" />  
- 추가 파라미터 없음
- 두 벡터의 차원이 반드시 같아야 함
- 두 벡터가 비슷한 방향을 가리킬수록 높은 스코어

#### 2. Multiplicative Attention (가중 내적)
<img width="256" height="34" alt="image" src="https://github.com/user-attachments/assets/e64a5831-29e3-48b2-ab15-598a366bc8e5" />  
- W​는 학습되는 가중치 행렬
- 인코더와 디코더 차원이 달라도 됨
- Dot-product보다 표현력이 높음

#### Additive Attention (Bahdanau Attention)
<img width="386" height="51" alt="image" src="https://github.com/user-attachments/assets/58a46772-690c-4b8a-92c8-b5841743b314" />  
- W1​, W2​: 학습되는 가중치 행렬
- v: 학습되는 가중치 벡터
- 두 hidden state를 더한 후 비선형 변환을 적용
- 가장 표현력이 높으며, 원래 Bahdanau 논문에서 제안된 방식

### Attention 예제 풀이
<img width="1110" height="565" alt="image" src="https://github.com/user-attachments/assets/93774ce6-2573-4c1b-9268-f4e6c3e1b847" />

<img width="649" height="511" alt="image" src="https://github.com/user-attachments/assets/417a22da-be7a-4b35-a22c-894dcec8bda5" />

### Attention의 효과와 의의
- 번역 성능 향상: attention 추가만으로 BLEU 스코어가 크게 향상
- 병목 문제 해결: 기존에는 h_enc 하나를 통해서만 소스 정보를 전달했지만, attention을 통해 디코더가 소스 문장의 모든 위치에 직접 접근할 수 있dma
  - 이는 더 짧은 경로의 역전파를 가능하게 하여 기울기 소실 문제도 완화
- 정렬(Alignment) 학습: 소스-타겟 단어 간의 대응 관계를 명시적으로 지정하지 않아도, **학습을 통해 자동으로 정렬을 발견**
- 해석 가능성 향상: Attention distribution을 시각화하면 모델이 어떤 소스 단어를 보고 타겟 단어를 생성했는지 확인할 수 있음 -> NMT의 블랙박스 문제를 부분적으로 해소





















































