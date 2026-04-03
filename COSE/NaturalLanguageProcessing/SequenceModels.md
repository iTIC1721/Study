# 🔊Sequence Models

3/27 수업

---

Sequence Model = 순서(sequence)를 가지는 데이터를 모델링하는 방법

ex)  
- Part-of-Speech Tagging (POS tagging): 문장을 단어 단위로 labeling
```
She sells seashells on the seashore
->
She/PRP sells/VBZ seashells/NNS on/IN the/DT seashore/NN
```
- Named Entity Recognition (NER): 단어 sequence에 의미 label을 붙이는 문제
```
Barack Obama → Person
United States → Location
January 20, 2009 → Date
```

#### NLP Pipeline에서의 위치
```
Text → Tokenizer → POS Tagger → Parser → NER → ...
```
여러 단계에서 핵심 역할

## POS Tagging
Part-of-Speech Tagging  
단어별로 어떤 품사(= 단어의 문법적 역할)인지 labeling

### 품사의 종류
크게 두 가지로 나눌 수 있음
- Closed class: 종류가 거의 추가되지 않는 단어들, ex) 전치사 (in, on), 관사 (the, a)
- Open class: 계속 새로운 단어가 추가되는 단어들, ex) 명사, 동사

### Ambiguity
같은 단어가 다른 품사가 될 수 있음
```
The man bought a boat  → man = NN
The old man the boat  → man = VBP
```
문맥에 따라 결정되는 경우가 있음

```
back/JJ → 형용사
back/NN → 명사
back/VB → 동사
back/RB → 부사
```
하나의 단어가 여러 역할이 가능한 경우도 있음

==> 이러한 경우들 때문에 단어를 독립적으로 보는 모델은 품사 분류 어려움 => sequence model 필요  
만약 단순한 방식(baseline)으로 "가장 많이 나온 품사를 할당"한다면 정확도가 매우 낮음

### Types vs Tokens
Type: 중복을 제거한 단어 집합  
Token: 전체 단어 집합 (중복 포함) 

## Hidden Markov Model (HMM)

### Markov assumption
P(st​∣s1​,s2​,...,st−1​) ≈ P(st​∣st−1​)

▶ 바로 직전의 상태만 보기

#### 구성 요소
상태 집합: S={1,2,...,K}  
초기 확률 = π(s)  
전이확률 = P(st​∣st−1​)  
▶ 시퀀스 확률: P(s1​,s2​,s3​) = π(s1​) ⋅ P(s2​∣s1​) ⋅ P(s3​∣s2​)

### HMM의 핵심 아이디어
Hidden + Observed 구조
```
s1   s2   s3   s4   (Hidden states = POS tags)
 |    |    |    |
o1   o2   o3   o4   (Observed words)
```
tag → word의 관계 생성

#### 구성 요소
상태: st (POS tag)  => hidden  
관측: ot (word)     => observe  
초기 확률 = π(s1​)  
전이 확률 = P(st​∣st−1​)  
출력 확률(이 품사에서 이 단어가 나올 확률) = P(ot​∣st​)

#### 두 가지 가정
✔ (1) Markov assumption  
P(st∣s1,...,st−1) ≈ P(st∣st−1)

✔ (2) Output independence  
P(ot∣s1,...,st) ≈ P(ot∣st)

#### 최종 확률
P(S,O) = π(s1​) * P(o1​∣s1​) * ∏_(i=2~n) ( ​P(si​∣si−1​) * P(oi​∣si​) )

dummy state s0 = φ를 사용하면:  
P(S,O) = ∏_(i=1~n) ( ​P(si​∣si−1​) * P(oi​∣si​) )      ∵ π(si) = P(s1∣​φ)

#### 전이 확률과 출력 확률은 어떻게?
=> Maximum Likelihood Estimation (MLE)

전이 확률: P(si | sj) = Count(si, sj) / Count(sj)  
출력 확률: P(o | s) = Count(o, s) / Count(s)

## Decoding with HMMs

### 우리가 풀어야 하는 문제
관측된 word sequence O가 주어졌을 때:

S∗ = argmax_S( ​P(S∣O) )                => P(S | O)를 가장 크게 만드는 S를 구하기  
   = argmax_S( ​P(O∣S) * P(S) )    (P(O)는 상수값이므로 제거)  
   = **arg​max_s1\~sn(​ ∏_(i=1\~n) ( ​P(si​∣si−1​) * P(oi​∣si​) ) )**

#### brute force 접근
-> 탐색 수 = K^n (K = tag 개수 ≈ 45, n = 문장 길이 ≈ 10~20) => 현실적으로 불가능

#### greedy 접근
-> 각 step에서 st = argmax_s( ​P(s∣st−1​) * P(ot​∣s) ) 를 수행 -> 한 번에 하나씩 결정  
but 앞에서 잘못 선택 시 뒤에서 회복 불가 (local optimum)  
▶ 빠르지만 optimal 보장 없음 (실패)

### Viterbi Algorithm
=> Dynamic Programming: 지금까지의 최적 결과를 저장하면서 진행

M[i, j] = time i에서 state j로 끝나는 최적 경로 확률  
B[i, j] = [i, j] 상태로 오기 **이전 상태**

#### 알고리즘
1. Initialize  
첫 단어 o1은: M[1,s] = π(s) * P(o1​∣s)  
-> 모든 tag s에 대해 계산

2. Recursion  

M[i,j] = max( M[i−1,k] * P(sj​∣sk​) * P(oi​∣sj​) )  
-> 이전 상태 k 중 가장 좋은 경로 선택

- back pointer

B[i,j] = argmax_k​(...)  
-> 가장 좋은 경로를 만드는 이전 상태 k

3. Termination + Backtracking

sn∗ ​= argmax_k( ​M[n,k] )

이후 B 배열을 통해 역추적: sn → sn-1 → ... → s1

#### 결론

모든 경로를 다 보지 않고, 각 노드마다 **최고 경로 하나만 유지**

#### 시간복잡도

▶ O(n * K^2)  
n: 단어 수  
K: tag 수  

∵ 각 (i, j)에 대해 이전 K개 확인

#### Log trick

확률 곱 ∏ 수행 시 underflow 발생 가능성 있음

-> log를 씌워 합으로 계산

```
logM[i,j] = max( logM[i-1,k] + logP(sj | sk) + logP(oi | sj) )
```

#### Beam Search
K가 크면 K^2가 너무 크므로 시간 너무 오래 걸림  
-> 상위 β개만 유지하여 최적화

▶ 시간복잡도 = O(nKβ)

빠르지만, 정확도 약간 손실


## Maximum Entropy Markov Models (MEMM)

**HMM의 근본적인 한계**
1. 현재 단어만 볼 수 있고, 주변 단어는 활용 못함
2. 단어가 tag만 보고 **생성**된다고 가정 -> 현실과 다름
3. feature(단어 대문자 여부, 이전 단어가 숫자인지, suffix가 -ing 등) 활용 불가능

**MEMM의 핵심 아이디어**  
전체 문장을 보고 확률 계산!

P(S∣O) = ∏_i=1~n(​ P(si​∣si−1​, **O**) )  
O = 전체 단어 집합 {o1, o2, o3, ..., on}

### MEMM

feature와 weight 고려 => P(si​=s∣si−1​, O) ∝ exp( w * f(s,si−1​,O,i) )  

정규화하면 =>
Bigram MEMM : P(si​=s∣si−1​, O) = exp( w * f(s,si−1​,O,i) ) / Σ_s'=1~K( exp( w * f(s',si−1​,O,i) ) )  
s, si-1, si-2로 늘리면 Trigram MEMM, 하나 더 늘리면 4-gram MEMM...

### feature

feature: 사람이 정의하는 정보  
=> f(si​,si−1​,O,i)
```
ex)
si = VB and oi-2 = "Janet"
si = VB and oi-1 = "will"
si = VB and si-1 = MD
```
=> 각각의 feature는 1 or 0 값으로 나타내어짐

#### weight
각 feature에는 weight가 있음  
높을수록 중요한 feature

### Decoding 목표
S∗ = argmax​_S( P(S∣O) )  
   = argmax_S( ∏_i( P(si | si-1, O) ) )  
=> Verterbi 사용 가능  
=> M[i,j] = max( M[i-1,k] * P(si=j | si-1=k, O) )

#### HMM과 비교
시간복잡도는 동일하게 O(n * K^2)이지만,  
MEMM은 feature 수에 따라 비용 증가


## Conditional Random Fields (CRFs)

목표: 가장 확률이 높은 전체 Tag Sequence를 선택

P(S|O) = exp( w * f(S, O) ) / Σ_S'( exp( w * f(S', O) ) )  
         = exp( w * f(S, O) ) / Z(O)

f(S, O): 전체 sequence에 대한 feature  
w: weight  
Z(O): 모든 sequence에 대한 합

### Features
각각의 Fk ∈ f는 global feature

P(S|O) = exp( Σ_k=1~m( wk * Fk(S, O) ) ) / Σ_S'( exp( Σ_k=1~m( wk * Fk(S', O) ) ) )  

Fk = ∑_i=1~n( fk​(si−1​,si​,O,i) )  
global feature = local feature들의 합
```
fk​(si−1​,si​,O,i) = 1  if si-1 = DT and si = NN
=> Fk = DT->NN 등장 횟수
```

### Decoding
S* = argmax_S( P(S|O) )  
   = argmax_S( exp( w * f(S,O) ) )        Z(O)는 상수이므로 제거  
   = argmax_S( ∑∑( wk * fk(si-1,si,O,i) ) )  
=> 이전과 동일하게 Viterbi 이용하여 계산  


#### MEMM과 비교
Z(O) 계산이 필요하므로 CRF가 더 비쌈
















