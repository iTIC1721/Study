# 🔊Language Model

3/6 수업

---
Language Model: 단어 조합의 **확률**을 계산하는 모델

## Chain Rule
조건부확률

P(w1, w2, w3, ..., wn) = P(w1) * P(w2 | w1) * P(w3 | w1, w2) * ... * P(wn | w1, w2, ..., w(n-1))  
ex) `cat and dog` => `P(cat and dog) = P(cat) * P(and | cat) * P(dog | cat and)`

P(A) = (A가 일어나는 결과의 수) / (가능한 결과의 수)  
ex) `P(dog | cat and) = count(cat and dog) / count(cat and)`

### Markov Assumption
어휘 집합의 크기가 V이고, 길이가 n인 시퀀스를 만든다고 가정  
-> n개의 단어 각각에서 선택할 수 있는 경우는 V가지 -> 총 V^n가지 경우의 수  
> *경우의 수가 너무 많음!*

-> Markov Assumption: 최근 단어들만 고려  
-> kth order Markov: 마지막 k개의 단어만 고려해서 확률 계산

## n-gram

n개의 이어진 단어의 그룹

예)
```
Unigram (n = 1)    I love NLP → [ "I", "love", "NLP" ]
Bigram (n = 2)     I love NLP → [ "I love", "love NLP" ]
Trigram (n = 3)    I love NLP → [ "I love NLP" ] 
```

### n-gram에서 확률 계산
unigram => `P(w1 w2 w3 ... wn) = P(w1)P(w2)...P(wn)`  
bigram => `P(w1 w2 w3 ... wn) = P(w1)P(w2 | w1)P(w3 | w2)...P(wn | w(n-1))`  
...

> n이 커질수록 정확해지지만, 계산 코스트가 늘어남


## Generating from a Language Model

ex) bigram에서 문장 생성  
첫 단어 생성: w1 ~ P(w)  
다음 단어: w2 ~ P(w | w1)  
다음 단어: w3 ~ P(w | w2)  
계속 반복...

### 생성 방법
1. Greedy: 단어들 중 가장 확률이 높은 것 선택

2. Sampling
- Top-k: 확률 상위 k개 중 선택
  - ex) 1등 단어의 확률이 0.99이고 나머지가 0.01 같은 경우는 상위 k개에서 선택하는 것이 적절하지 않을 수 있음
- Top-p: 상위부터 확률 합이 p가 될 때까지 선택 (처음으로 p를 넘길 때까지)

ex) 다음 단어로 올 확률이 다음과 같을 때
|단어|확률|
|-|-|
|for|0.4|
|to|0.25|
|with|0.17|
|and|0.13|
|by|0.05|

Greedy -> for  
Top-k Sampling (k = 3) -> for / to / with 중 선택  
Top-p Sampling (p = 0.6) -> for / to 중 선택


## Evaluating a Language Model

LM의 성능 평가 방법
1. 사전 학습된 NLP Tasks로 성능 평가
   - 비용이 크고 느림 → 보통 외부 benchmark 서비스 사용

2. Intrinsic Evaluation
Perplexity (ppl) 사용

Perplexity: 다음 단어를 평균적으로 몇 개의 후보 중에서 고르는지 (=> 얼마나 다음 단어를 잘/효율적으로 예측하는지)  
낮을수록 좋은 성능

ppl = `P(w1, w2, ..., wn)^(-1/n)`  
<=>  
ppl = `e^x` where `x = -1/n * Sigma_i(log P(wi | w1 w2 ... w(i-1)))`
> log P(wi | w1 w2 ... w(i-1)) = **cross entropy**


## Smoothing

n-gram의 문제점
1. Sparsity: 훈련 데이터에 없는 n-gram이 입력되면 확률이 0이 됨 => ppl 계산 불가능
2. Long tail problem: 몇 개의 단어만 매우 많고 대부분의 단어는 매우 드묾

**Smoothing**: 모든 n-gram 확률이 0이 아니게 만들기
### 1. Additive (Laplace)
모든 count에 α 추가 (보통 α = 1)  
기존: P(w2 | w1) = count(w1 w2) / count(w1)
-> α 추가: P(w2 | w1) = (count(w1 w2) + α) / (count(w1) + α * |V|)

### 2. Discounting
자주 나온 n-gram의 확률을 조금 줄여서 확률이 0인 n-gram에 분배  
ex) `3 a / 2 b / 1 c / 1 d / 0 others...` --> `2.5 a / 1.5 b / 0.5 c / 0.5 d / 2 others...`

#### 2-1. Absolute Discounting
`Count*(x) = Count(x) - 0.5`로 정의!  
모든 Count에서 0.5씩 빼서 남은 unseen n-gram에 분배  

### 3. Interpolation
여러 n-gram 모델을 결합하여 사용
ex) P(w3 | w1 w2) = λ1 * P(trigram) + λ2 * P(bigram) + λ3 * P(unigram)
이때 **λ1 + λ2 + λ3 = 1** (λ들의 합이 1이 되도록 함)

장점: 안정적이고 성능이 좋음

- λ를 어떻게 정함?
1.	훈련 세트에서 n-gram 확률을 추정
2.	훈련/검증 세트에서 확률을 최대화하는 하이퍼파라미터 λ를 튜닝
3.	테스트 세트에서 평가







