# 🔊Recurrent Neural Networks

4/3 수업

---


## Feed-Forward Neural Networks
입력층 → 은닉층 → 출력층으로 한 방향 연결.  
사이클 없음, 출력이 아래로 돌아오지 않음  
Fully-connected layer (한 층의 모든 유닛이 다음 층의 모든 유닛과 연결)

<img width="1407" height="562" alt="image" src="https://github.com/user-attachments/assets/e5644649-85b8-4bba-8a3b-2ded1f1a338d" />

<img width="1297" height="570" alt="image" src="https://github.com/user-attachments/assets/c58a11e4-beef-4199-b94f-6a4e548bf9cb" />

<img width="1405" height="701" alt="image" src="https://github.com/user-attachments/assets/add020e1-27d7-48eb-bd5b-345c3f08ac59" />

<img width="397" height="77" alt="image" src="https://github.com/user-attachments/assets/1468b299-817b-4077-8db7-9f86d46ebdf2" />

※ PyTorch에서는 Backpropagation 과정을 코드 한 줄로 처리 가능!
```python
outputs = net(inputs)
loss = criterion(outputs, labels)
loss.backward()      # <-- 역전파
optimizer.step()
```

### Image와 Text Input의 차이점
**Image**
- 고정 크기 입력
- continuous values

**Text**
- **가변 크기 입력**
- discrete words

### Text Clasification에서의 NN 사용
가변 크기 입력을 어떻게 처리하는가?

#### 1. 수동 feature 입력
단어 수, 긍정 어휘 수 등을 벡터로 만들어 입력 x로 설정

<img width="633" height="328" alt="image" src="https://github.com/user-attachments/assets/0e1054c5-7789-4c2a-98e8-a990f71b8a32" />

#### 2. 단어 임베딩 Pooling
모든 단어 임베딩의 평균/합/최댓값으로 **고정 크기 벡터** 생성 (Deep Averaging Network)

<img width="683" height="441" alt="image" src="https://github.com/user-attachments/assets/0dcdc83d-6b66-4ed1-bb7c-a2bc593f246c" />

단어 임베딩 행렬 E는 Training Data가 크면 함께 학습시키지만, 작으면 함께 학습시키지 않음 (overfitting 방지)

#### 장점
- 가변 길이 입력 처리 가능
- 특징 자동 학습
- 임베딩 유사도로 일반화

#### 단점
- **단어 순서 정보 완전 소실**

### Neural Language Model
**N-gram 모델의 단점**:  
context 길이 제한 (ex: trigram → 3개만 기억)  
window 커지면 데이터 sparsity 커짐 + 경우의 수 과도하게 증가

--> **Neural Language Model**!!

핵심 아이디어: 확률을 직접 세지 않고 **NN으로 확률 분포를 근사**. 임베딩을 사용하여 **유사 문맥(good ≈ great)**에서 비슷한 예측
Feed-forward 구조: 이전 m개 단어만 임베딩으로 연결. 

**한계:**
- m 크기에 따라 가중치 W 선형 증가, 위치마다 다른 패턴 학습
- 긴 문맥 못 봄

## Recurrent Neural Networks

핵심 아이디어: **같은 가중치 w**를 시간 축에서 **반복 적용**해 **가변 길이 입력 처리**

<img width="1021" height="316" alt="image" src="https://github.com/user-attachments/assets/bafa9e04-28ef-4fa0-965b-5bea3059c4fa" />

<img width="1222" height="621" alt="image" src="https://github.com/user-attachments/assets/689cb2dc-76a2-4f23-abe0-75b15deb9727" />

W: 이전 은닉층 -> 현재로의 가중치
U: 입력 -> 현재로의 가중치
b: 편향

<img width="847" height="245" alt="image" src="https://github.com/user-attachments/assets/f64f2605-8020-4004-92c9-e66d9eadc8fa" />

### Recurrent Neural Language Models (RNNLMs)
<img width="1460" height="127" alt="image" src="https://github.com/user-attachments/assets/0c57d67e-7ebf-4ba4-b6fa-c768604337bc" />

<img width="1310" height="467" alt="image" src="https://github.com/user-attachments/assets/f1b78ee3-a803-42c6-a4f6-8248d1aae951" />

#### Weight tying
**입력 임베딩 E와 출력 W_o 공유** (d = h일 때)  
-> 파라미터 수 감소, 성능 향상

### 장단점
**장점**:  
- 긴 입력 처리 가능
- context 누적
- 파라미터 효율적
**단점**:  
- 병렬화 어려움 (느림)
- long-term dependency 어려움

### Backpropagation Through Time
RNN을 time-step으로 펼쳐서 일반 backprop 적용

### Gradient 문제
#### Gradient Vanishing
- 먼 시점의 그래디언트 소멸 → 장기 의존성 학습 불가
- 가까운 패턴만 학습
- 해결: LSTM / GRU

#### Gradient Exploding
- 그래디언트가 폭발적으로 커짐 → 수렴 불안정
- 해결: Gradient Clipping (임계값 초과 시 스케일 다운)

## RNN Application and Variants
### 적용: 텍스트 생성
다음 단어를 계속 샘플링  
출력 → 다음 입력

<img width="660" height="507" alt="image" src="https://github.com/user-attachments/assets/01efaf3e-6b2d-486c-874e-a77c216212ae" />

### 적용: Sequence tagging
각 단어마다 label 예측 (ex: POS tagging)

### 적용: Text classification
마지막 hidden state → 전체 문장 의미

<img width="760" height="396" alt="image" src="https://github.com/user-attachments/assets/bb79490b-0a56-4450-bad1-8c8d6c3f26eb" />

### 변형: Multi-layer RNNs (다층 구조 RNN)
구조: i번째 RNN의 은닉 상태가 (i+1)번째 RNN의 입력  
실전 팁: 2~4층이 일반적으로 1층보다 성능 좋음. Transformer는 24층까지 skip-connection과 함께 사용

<img width="636" height="417" alt="image" src="https://github.com/user-attachments/assets/a866b0f1-e68f-4398-a405-c4f4a43ca79e" />

### 변형: Bidirectional RNNs (양방향 RNN)
<img width="872" height="442" alt="image" src="https://github.com/user-attachments/assets/e00cda8b-81c8-4154-9c9c-62f254513482" />

각 단어 표현에 좌우 문맥 모두 포함. "terribly"는 "the movie was" + "exciting !" 모두 필요

<img width="1352" height="513" alt="image" src="https://github.com/user-attachments/assets/382dd507-05e2-438e-ba1f-29fe01337cc0" />

<img width="760" height="201" alt="image" src="https://github.com/user-attachments/assets/7fcb5d59-8a3f-4669-b244-454f122333b5" />

## Long Short-Term Memory RNNs (LSTMs)
핵심 아이디어: 곱셈 대신 덧셈 + "게이트"로 정보 추가/삭제 제어 → 장기 의존성 학습  
Vanishing Gradient에 강함

<img width="1446" height="627" alt="image" src="https://github.com/user-attachments/assets/533a1f1b-a9d1-472b-a19c-88f1ebad9f2a" />

<img width="698" height="267" alt="image" src="https://github.com/user-attachments/assets/7258f123-2261-4f96-ab55-d34b5226f4b4" />

h_t 범위: -1 ~ 1

### Vanishing Gradient에 강한 이유
c_{t-1} → c_t 경로는 덧셈 연산. 역전파 시 그래디언트가 곱셈 없이 직접 흘러 "고속도로" 역할

<img width="1478" height="687" alt="image" src="https://github.com/user-attachments/assets/27bce94d-22f1-433b-8be8-fb1721354565" />

## Gated Recurrent Units (GRUs)
LSTM을 단순화한 RNN 변형

기본 아이디어: LSTM은 3개의 게이트(입력/망각/출력)와 별도의 셀 상태(c_t)를 가지지만, GRU는 이를 2개의 게이트로 줄이고 셀 상태 없이 은닉 상태(h_t) 하나만 사용

- 리셋 게이트 (r_t): 이전 은닉 상태를 얼마나 "무시"할지 결정. r_t = 0이면 과거를 완전히 무시하고 새로 시작
- 업데이트 게이트 (z_t): 이전 상태를 얼마나 "유지"할지 결정. LSTM의 입력 게이트와 망각 게이트를 하나로 합친 것

<img width="1267" height="606" alt="image" src="https://github.com/user-attachments/assets/a0aa30de-d2d2-4c98-a303-2e860f3225d0" />

<img width="790" height="312" alt="image" src="https://github.com/user-attachments/assets/b9bd53b4-6a51-460d-8924-b81ea78f2c57" />

<img width="847" height="256" alt="image" src="https://github.com/user-attachments/assets/7ae327a2-75f6-46f5-868f-7bdd004bcc00" />

### 실전 팁 (Practical Takeaways)
1. LSTM을 기본으로 사용
2. 그래디언트 클리핑 적용 (폭발 방지)
3. 가능하면 양방향(Bidirectional) RNN 사용
4. 2~4층 다층 RNN 사용. 깊으면 Skip Connection 고려
5. Simple RNN은 "Vanilla RNN"이라고도 불림 — 실전에서는 LSTM/GRU 선호















































