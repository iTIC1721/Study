# 🔊Text Classification

3/13 수업

---
Text Classification: 문서나 문장을 특정 카테고리(class)로 분류

문서 d와 클래스 집합 C={c_0, c_1, c_2, ...}가 있고, 문서를 분류하는 Classifier F가 있을 떄,  
**F(d)=c_n**

긍정 / 부정 감정 분석, 스팸 메일 필터 등 여러 분야에서 사용

## Text Classification 방법
1. **Rule-Based 방식**
```
IF document contains
[good, great, excellent]
→ Positive

IF email ends with
[ithelpdesk.com, makemoney.com]
→ Spam
```

**장점**
- 사람이 이해 가능
- 특정 분야에서 매우 정확

**단점**
- 규칙 설계가 매우 어렵다
- 새로운 데이터에 일반화 어렵다
- 유지 비용 높음

2. **Supervised Learning 방식**
머신러닝으로 학습

▶ **문서를 입력받아 클래스를 출력하는 함수 학습**

## Naïve Bayes Classifier
NLP에서 가장 대표적인 확률 기반 분류기

### Bayes Rule
P(c|d) = P(d)P(c) / P(d|c)​

### MAP Classification
가장 확률이 높은 클래스를 선택

```
c_MAP(가장 확률이 높은 클래스) = argmax( P(c|d) )  
→ Bayes rule 적용  
c_MAP = argmax( P(d|c)P(c) / P(d) )
→ 분모 P(d)는 모든 클래스에서 같으므로 제거 가능
따라서 c_MAP = argmax( P(d|c)P(c) )
```

### Bag of Words 가정
모든 조건부 확률 **P(w1​,w2​,...,wk​∣c)**을 계산하는 것은 현실적으로 너무 비용이 크므로,  
**모든 단어들은 서로 독립이다** 라는 가정을 사용

따라서 P(w1​,w2​,...,wk​∣c) = P(w1|c)P(w2|c)P(w3|c)...P(wk|c)

### LOG 사용
곱셈은 언더플로우 문제가 생길 수 있으므로, log를 사용하여 계산  
```
c = argmax( logP(c) ) + ∑​( logP(wi​|c) )
```

### 확률 추정
```
// 훈련 세트에 클래스 c인 문서가 얼마나 있는가
P(c) = Count(c) / n
```
```
// 클래스 c의 모든 단어들 중 단어 wi가 얼마나 나타나는지 (V = c의 모든 단어)
P(wi|c) = Count(wi, c) / ∑​_(w∈V)( Count(w​, c) )
```

### Data Sparsity 문제
훈련 세트에 없는 단어가 있으면 전체 확률도 망가짐

예)
단어 fantastic이 훈련 세트에 없다면, P(fantastic | positive) = 0  
→ 전체 확률도 망가짐

#### 해결법: Laplace Smoothing
```
// 분자에 α, 분모에 α|V|를 더함. 일반적으로 α = 1
P(wi|c) = (Count(wi, c) + α) / (∑​_(w∈V)( Count(w​, c) ) + α|V|)
```

### 최종 Naïve Bayes 과정
1. 전체 단어 `V`를 계산
2. `P(cj) = Count(cj) / n` 을 계산
3. `P(wi|cj) = (Count(wi, cj) + α) / (∑​_(w∈V)( Count(w​, cj) ) + α|V|)` 를 계산
4. 전체 문서 d = (w1, w2, ..., wk)에 대해,  
c_MAP = argmax P(c) * P(w1|c)P(w2|c)P(w3|c)...P(wk|c)


### 예시
Training Set
```
-    just plain boring
-    entirely predictable and lacks energy
-    no surprises and very few laughs
+    very powerful
+    the most fun film of the summer
```
Test Set
```
?    predictable with no fun
```

1. P(c) 계산:  
  P(-) = 3/5,  
  P(+) = 2/5
2. Stop word(정보량이 없는 단어들: the, with, is, and 등) 제거: predictable ~~with~~ no fun
3. P(wi|c) 계산(α=1):    
  P(predictable|-) = (1+1)/(14+20) = 2/34,  
  P(predictable|+) = (0+1)/(9+20) = 1/29,
  P(no|-) = (1+1)/(14+20) = 2/34,  
  P(no|+) = (0+1)/(9+20) = 1/29,
  P(fun|-) = (0+1)/(14+20) = 1/34,  
  P(fun|+) = (1+1)/(9+20) = 2/29
4. 최종 점수를 계산:  
   P(-) * P(S|-) = 3/5 * 2/34 * 2/34 * 2/34    
   P(+) * P(S|+) = 2/5 * 1/29 * 1/29 * 2/29  
   둘 중 큰 쪽 선택

### +) Binary Naive Bayes
일부 문제에서는 단어 등장 여부만 중요  
→ 단어별 count를 1로 clip해서 계산

## Supervised Learning Approach
머신러닝은 텍스트를 바로 처리 못하므로, **숫자 벡터**로 변환하여 계산

### Sparse Representation
모든 단어 풀에서 사용한 단어만 1로 하는 벡터
```
[w1, w2, w3, ..., w100000]이라고 하면,
이중 사용한 단어만 1로 하고 나머지는 0.
```
대부분의 값이 0이기 때문에 sparse(희소) 벡터라고 부름

### Rule-based Feature
단어 말고 다른 feature로 sparse representation

예)
| feature | 의미         |
| ------- | ---------- |
| f1      | URL 개수     |
| f2      | unique URL |
| f3      | email 주소 수 |
| f4      | 숫자+문자 단어   |
| f5      | 문자 단어      |
| f6      | 긴 단어 수     |

예시 메일
```
Subject: Congratulations! You've won a $1,000 gift card!

Hi there,

Claim your exclusive $1,000 gift card now! Just visit
the link below and follow the instructions:
http://winbig123.com
Hurry, this offer expires soon!

Check our terms and conditions here: 
https://winbig123.com/terms.

Don't miss out on this limited-time opportunity. Click 
here now: http://winbig123.com.

Best regards,  
The WinBigTeam.
```
feature vector
```
d = [3,2,0,2,20,0]
```

이는 Naive Bayes도 적용 가능
- P(d∣c)=P(f1​∣c)P(f2​∣c)...P(fn​∣c)

위의 예시에서  
P(d∣spam)=P(f1=3​∣c)P(f2=02∣c)P(f3=0​∣c)P(f4=2​∣c)P(f5=20​∣c)P(f6=0​∣c)

### TF-IDF (Term Frequency - Inverse Document Frequency)
해당 문서(컬렉션, corpus)에서 단어가 얼마나 중요한지를 나타내는 지표  
= TF * IDF

TF(Term Frequency): 문서에서 얼마나 자주 등장하는지   
```
TF(t) = 문서 d에서 t의 개수 / d의 전체 단어 수
```

IDF(Inverse Document Frequency): 여러 문서에서 공통으로 등장하는 단어의 가중치를 줄임
```
IDF(t) = log(N / df(t))
N: corpus에서 문서의 개수
df(t): t를 포함하는 문서의 수
→ 희귀한 단어일수록 IDF값 증가 (희귀한 단어를 사용한다는 것은 중요한 의미라는 뜻!)
```

## Logistic Regression

feature vector를 입력으로 받음

### 계산 과정
1. **Linear score 계산**:  

z = w ⋅ x + b

| 기호  | 의미         |
| --- | -------------- |
| (x) | feature vector |
| (w) | weight vector  |
| (b) | bias           |
| (z) | linear score   |

z = w1​x1 ​+ w2​x2 ​+ ... + wk​xk ​+ b  
▶ feature들의 가중합!

2. **Sigmoid 함수**

σ(z) = 1 / (1 + e^(−z))

→ y' = P(y=1∣x) = σ(w ⋅ x + b)  
▶ 출력 범위 = 0 ~ 1  
▷ 확률로 해석 가능!

3. **Classification 결정**

ex) y = 1 (y' ​>= 0.5)
    y = 0 (y' < 0.5)


### Loss Function
예측이 정확할수록 작은 값을, 틀렸을수록 큰 값을 반환하는 함수  
모델 학습을 위해 필요함

Loss = -∑( yi * log(yi') + (1 - yi)log(1 - yi') )

범위: 0 ≤ Loss < ∞

### Optimization
우리의 목표: Loss가 최소가 되는 w와 b 값 찾기

#### Gradient Descent
yi' = σ(w ⋅ xi + b),  
Loss = -∑( yi * log(yi') + (1 - yi)log(1 - yi') )이므로,  
Gradient_w: dLoss(w,b) / dwi = ∑( (yi' - yi)xij )
    - xij = feature vector x의 j번째 값

Gradient_b: dLoss(w,b) / db = ∑( yi' - yi )

Gradient를 이용해 w, b 조정
w = w - η * Gradient_w
b = b - η * Gradient_b
- η: learning rate


### Regularization
Overfitting를 방지하는 방법

#### L2 regularization

θ∗ = argmax[ ∑logP(yi​∣xi​) − α∑(θj^2)​ ]


## Multiclass Logistic Regression
binary 클래스만이 아닌 여러 클래스에서의 logistic regression

Sigmoid 함수 대신 Softmax 함수를 사용
```
Softmax(zi​) = e^(zi) / ∑_j( e^zj​ )
```

Classifier:  
```
P(y=c|x) = e^(wc * x + bc) / ∑_j( e^(wj * x + bj) )
```

Loss Function:
```
Loss = -∑_c( ​1[y=c] * logP(y=c|x) )

※ 1[y=c] = y=c일때 1을 반환하는 함수
```

Gradient:
```
Gradient = dLoss / dwc = -(1[y=c] - P(y=c|x))x
```

















