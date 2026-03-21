# 🔊Word Embedding

3/20 수업

---

의미 유사성 기반 모델  
각 단어를 벡터로 나타냄 → 비슷한 의미의 단어는 벡터 공간 상에서 가까움

#### 기존 방법들의 단점
1. Naive Bayes 모델: 단어는 그냥 문자열/인덱스일 뿐, 의미 관계를 반영하지 못함
2. rule-based 모델: 어느 정도 맥락을 기반으로 분류하지만, 정확히 같은 단어끼리만 인식하므로 비슷하지만 다른 단어들에 대해서는 반영하지 못함
(ex. terrible ≠ horrible)

## Word Representation
### Distributional Hypothesis

> "단어의 의미는 그 사용에 있다"
> "같은 문맥에 등장하면 비슷한 의미"

비슷한 문맥 → 비슷한 의미

```
예시) Ongchoi

Ongchoi is delicious sauteed with galic
Ongchoi is superb over rice
Ongchoi leaves with salty sauces

→ Ongchoi는 "채소"라고 의미 추론 가능
```

### Co-occurrence
위와 같은 의미 추론을 어떻게 할 수 있을까?

→ "주변 단어 빈도 벡터"  
주변 단어를 세서 벡터로 표현 (예: 좌우 4단어 context)


그럼 벡터 사이의 의미 유사도는 어떻게 측정?

→ "cosine similarity"  
```
cos(u,v) = u⋅v / |u||v|
```

※ 그냥 u⋅v로 계산하지 않는 이유: 빈도 수가 많은 단어는 벡터의 크기가 크기 때문에 크기를 제거하고 방향만 비교  
※ cos(u,v)의 범위: [-1, 1]

◆ 벡터 방식이 문제점: "the", "it", "they" 등 자체 의미는 적으면서 빈도 수가 매우 높은 단어들이 전체 벡터 공간을 지배  
▶ 해결책: raw count(단순 빈도)로 세는 대신 weighted function 사용

### PMI
Pointwise Mutual Information

두 단어가 따로 등장하는 것보다 같이 등장하는 경우가 많으면 값 ↑  
ex) cherry와 pie가 각각 따로 등장하는 것보다 서로 붙어서 등장하는 경우가 더 많음  
→ "PMI(cherry, pie)의 값 높음!"

```
PMI = log2( P(x,y) / P(x)P(y) )
```

### PPMI
Positive Pointwise Mutual Information

PMI의 문제: 음수 값이 많아 신뢰 어려움  
→ 음수를 제거하여 신뢰도 상승

```
PPMI = max(PMI, 0)
```

### Dense Vector

기존 co-occurrence 벡터의 문제: 차원 = vocab 크기이므로 너무 sparse함(0이 많음) → 메모리 낭비가 심하고 학습이 어려움
→ 적은 차원으로 일반화 가능한 벡터 구조를 찾자

1. Count 기반  
- 기존 벡터에 SVD(행렬 분해)를 수행해 압축

2. Prediction 기반
- **모델이 직접 벡터 학습**
- word2vec, Glove, FastText 등


## Word Embedding (word2vec)

Word Embedding의 정의: 모델이 자동으로 벡터를 배우게 함

입력 - 큰 텍스트 corpus (수십억 단어), 단어 V, 차원 수 d (e.g. 300개)  
출력 - 함수 f : V → ℝ^d (단어를 벡터로 매핑)

각 차원은 정해진 의미가 없고(black box), 전체 벡터가 의미를 가짐  
이러한 embedding은 데이터의 편향까지 학습하기도 함 (ex. man : programmer :: woman : homemaker)

### skip-gram
중심 단어 → 주변 단어 예측

```
예시 문장
problems turning into banking crises

중심 단어: "into"

예측 대상:
problems
turning
banking
crises

목표: P(context∣center) → 중심 단어 근처에 뭐가 나올까?
```

- 학습 데이터 생성  
위의 예시에서 문장을 다음과 같이 변환 →
```
(into, problems)
(into, turning)
(into, banking)
(into, crises)
```

- Objective Function  
모델의 성능 평가 함수  
![목적함수]()

▶ 𝑃(𝑤_𝑡+𝑗 | 𝑤_𝑡;𝜃)를 어떻게 정의하는가?

두 종류의 벡터를 사용 - u_a, v_b
- u_a: a가 중심 단어일때의 벡터
- v_b: b가 주변 단어일때의 벡터

두 벡터의 내적 u_a ⋅ v_b은 a가 b와 함께 등장할 확률을 나타냄
![softmax]()

- word2vec 계산
→ 교재 pdf (Word Embedding) 44p~51p 참조

### Softmax 알고리즘의 문제점
k∈V(전체 단어 셋)에 대해서 전부 계산을 실시하므로, ​계산이 매우 느림

▶ 해결: Negative Sampling  
→ 전체 단어 대신 랜덤한 일부만 보자

positive:  
(t, c)

negative:  
(t, random word)

▶ Objective Function:  
log( σ(ut​⋅vc​) ) + ∑ log( σ(−ut​⋅vj​) )

→ 실제 같이 나오는 쌍은 가까워지게, 다른 랜덤 쌍은 멀어지게

## 다른 Word Embedding 알고리즘
### CBOW
context 평균에서 center 단어를 예측  
더 빠르지만 덜 정확함

### GloVe
global co-occurrence 사용

### FastText
단어를 subword로 분해  
ex) unhappiness = un + happy + ness

장점:  
- OOV 해결
- morphology 반영

## Embedding 성능 평가
- Extrinsic Evaluation  
실제 task로 성능을 측정  
→ 오래 걸리지만 중요한 성능 측정 방식

- Intrinsic Evaluation  
간단한 subtask로 성능을 측정
→ 빠르지만 실제 성능을 보장하지는 못함

















