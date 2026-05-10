# NLP Lecture 09 — 문맥화된 표현과 사전학습 완전 정리

## 목차

1. [배경 지식: 왜 이 주제가 중요한가](#1-배경-지식-왜-이-주제가-중요한가)
2. [word2vec의 한계](#2-word2vec의-한계)
3. [ELMo: 문맥화된 단어 임베딩](#3-elmo-문맥화된-단어-임베딩)
4. [사전학습과 파인튜닝 패러다임](#4-사전학습과-파인튜닝-패러다임)
5. [GPT](#5-gpt-generative-pre-training)
6. [BERT](#6-bert-bidirectional-encoder-representations-from-transformers)
7. [GPT vs BERT 비교](#7-gpt-vs-bert-비교)
8. [Post-BERT 모델들](#8-post-bert-모델들)
9. [핵심 개념 한눈에 보기](#9-핵심-개념-한눈에-보기)
10. [시험 예상 문제 & 답](#10-시험-예상-문제--답)

---

## 1. 배경 지식: 왜 이 주제가 중요한가

### 단어를 벡터로 표현한다는 것

컴퓨터는 텍스트를 그대로 이해하지 못합니다. 그래서 단어를 **숫자 배열(벡터)** 로 바꿔야 합니다.

- 예: `e(play) = [-0.224, 0.130, -0.290, 0.276]`
- 비슷한 의미의 단어는 벡터 공간에서 가까이 위치하게 됩니다.

기존의 word2vec은 이 벡터를 잘 만들었지만, 결정적인 문제가 있었습니다.

---

## 2. word2vec의 한계

### 핵심 문제: 단어 하나 = 벡터 하나 (정적 임베딩)

word2vec은 훈련이 끝나면 각 단어에 고정된 벡터가 딱 하나 할당됩니다.

```
e(bank) = [-0.1, 0.5, 0.3, ...]  ← 언제나 이 값
```

### 문제가 되는 이유: 다의어

| 문장 | "bank"의 의미 |
|------|--------------|
| "a bank can hold the investments in a custodial account" | 금융기관 (은행) |
| "as agriculture burgeons on the east bank" | 강변 (둑) |

두 문장의 "bank"는 완전히 다른 의미인데, word2vec은 둘 다 **똑같은 벡터**를 내보냅니다.

### word2vec vs ELMo 구조 차이

| 항목 | word2vec | ELMo |
|------|----------|------|
| 벡터 생성 방식 | 룩업 테이블 (미리 저장) | 추론 시 동적 계산 |
| 문맥 반영 | 불가 (항상 동일) | 가능 (문장마다 다름) |
| 다의어 처리 | 불가 | 가능 |
| 미등록 단어 | 처리 불가 | 문자 단위 CNN으로 처리 |
| 모델 가중치 | 훈련 후 고정 | 훈련 후 고정 (벡터는 매번 계산) |

> **핵심 포인트**: ELMo의 모델 가중치(파라미터) 자체는 고정되어 있습니다. 변하는 것은 그 고정된 모델이 매번 다른 문장을 입력받아 계산해내는 **출력값(벡터)** 입니다.

---

## 3. ELMo: 문맥화된 단어 임베딩

> Peters et al., 2018

### 핵심 아이디어

**"단어의 벡터를 문장 전체 문맥에 따라 다르게 만들자"**

함수 표현: `f: (w₁, w₂, ..., wₙ) → x₁, ..., xₙ ∈ ℝᵈ`

즉, 단어 하나를 독립적으로 보는 게 아니라 **문장 전체를 입력**으로 받아 각 단어의 벡터를 계산합니다.

### 구조

```
[입력 문장] → [문자 단위 CNN 임베딩]
                        ↓
              [양방향 LSTM 1층 (Forward + Backward)]
                        ↓
              [양방향 LSTM 2층 (Forward + Backward)]
                        ↓
              [출력: 다음 단어 예측 (언어모델 학습)]
```

- **Forward LM**: 왼쪽 → 오른쪽으로 문장을 읽으며 다음 단어 예측
- **Backward LM**: 오른쪽 → 왼쪽으로 문장을 읽으며 이전 단어 예측

### ELMo 임베딩 계산 공식

```
ELMo_k^task = γ^task × Σ(j=0 to L) s_j^task × h_{k,j}^LM
```

- `h_{k,j}^LM`: k번째 단어의 j번째 층 은닉 상태
- `s_j^task`: 각 층에 대한 **가중치** (태스크마다 학습됨)
- `γ^task`: 전체 스케일 조정 파라미터

즉, **입력 임베딩 + LSTM 1층 + LSTM 2층**, 세 층의 출력을 태스크에 맞게 가중 합산합니다.

### 왜 여러 층을 가중 평균하나?

층마다 담는 정보가 다르기 때문입니다.

| 층 | 담는 정보 |
|----|----------|
| 1층 (낮은 층) | 구문(문법) 정보 → POS 태깅에 유리 |
| 2층 (높은 층) | 의미(semantics) 정보 → 단어 의미 구별(WSD)에 유리 |

태스크에 따라 어떤 층이 더 중요한지 다르므로, **가중치를 학습으로 결정**합니다.

### 왜 양방향(Forward + Backward)을 모두 쓰나?

"He went to the **bank**."에서 "bank"의 의미를 파악하려면 앞 단어("went to the")뿐 아니라 뒤 단어("to make a deposit" 또는 "on the river")도 봐야 합니다. **양방향성**이 언어 이해에 매우 중요합니다.

### ELMo 학습 규모

- 데이터: 1B Word Benchmark, 10 에포크
- 학습 시간: NVIDIA GTX 1080 GPU 3개로 2주

### ELMo 사용 방식 (Feature-based)

ELMo는 **특징 기반(feature-based)** 접근법입니다.

```
[ELMo로 만든 문맥화된 임베딩]
            ↓
  [기존 NLP 모델의 입력으로 사용]
            ↓
       [태스크 수행]
```

기존 모델 구조를 바꾸지 않고, ELMo 벡터를 입력에 추가(concat)만 합니다.

---

## 4. 사전학습과 파인튜닝 패러다임

### 비유로 이해하기

> 요리학교에서 **칼질, 불 조절, 맛 균형**(기본기)을 수년간 익힌 요리사는, 이탈리안 레스토랑에 취직했을 때 파스타 레시피만 며칠 배우면 바로 일할 수 있습니다.

- **사전학습(pre-training)** = 요리학교에서 기본기 익히기 (대규모 텍스트로 언어 전반 학습)
- **파인튜닝(fine-tuning)** = 특정 레스토랑 레시피 배우기 (소량의 태스크별 데이터로 추가 학습)

### 핵심 원리

```
[1단계: 사전학습]
대규모 비지도 데이터 (인터넷, 책 등)
        ↓ 언어의 기본기 학습 (문법, 사실, 문맥 이해)
   사전학습된 모델

[2단계: 파인튜닝]
소량의 태스크별 데이터 (레이블 있음)
        ↓ 빠른 적응
   태스크 특화 모델
```

### ELMo vs GPT/BERT: 두 가지 접근법

| 접근법 | 대표 모델 | 설명 |
|--------|----------|------|
| 특징 기반(feature-based) | ELMo | 사전학습된 표현을 기존 모델 입력으로만 활용 |
| 파인튜닝(fine-tuning) | GPT, BERT | 사전학습 모델 전체를 태스크에 맞게 재조정 |

---

## 5. GPT (Generative Pre-Training)

> Radford et al., 2018

### 핵심 구조

- **Transformer 디코더** 기반
- **단방향 (왼쪽 → 오른쪽)**: 앞에 나온 단어들만 보고 다음 단어 예측
- 512 BPE 토큰 단위로 학습 (문장 단위가 아닌 더 긴 텍스트)

### 사전학습 목적함수: 언어모델

```
입력:  So   long   and   thanks   for
예측: long   and  thanks   for     all
손실:  -log y_long  -log y_and  ...  = (1/T) Σ L_CE
```

다음 단어를 맞히는 것 자체가 학습 목표입니다. **정답 레이블이 별도로 필요 없는** 자기지도학습입니다.

### 파인튜닝: 태스크별 적응

GPT는 전체 파라미터를 다양한 하위 태스크에 맞게 파인튜닝합니다.

| 태스크 | 입력 형식 |
|--------|----------|
| 분류 | [Start] Text [Extract] → Linear |
| 함의(Entailment) | [Start] Premise [Delim] Hypothesis [Extract] → Linear |
| 유사도 | 두 방향으로 각각 입력 → Linear |
| 다지선다 | 각 선택지마다 입력 → Linear × N |

### GPT의 한계

단방향이라 왼쪽 문맥만 보므로 문장 **이해** 태스크에서는 BERT보다 성능이 낮습니다.

---

## 6. BERT (Bidirectional Encoder Representations from Transformers)

> Devlin et al., 2019

### 핵심 구조

- **Transformer 인코더** 기반
- **양방향**: 앞뒤 문맥을 동시에 봄
- 두 가지 사전학습 목적함수: **MLM + NSP**

### 사전학습 목적함수 1: MLM (Masked Language Modeling)

**"왜 일반 언어모델을 쓰지 않나?"**

양방향 모델에서 일반 언어모델(다음 단어 예측)을 쓰면, 예측하려는 단어를 모델이 이미 볼 수 있어서 학습이 의미 없어집니다.

**해결책**: 입력 토큰의 일부를 가리고 예측하게 합니다.

```
입력:  "the man went to [MASK] to buy a [MASK] of milk"
예측:                   store              gallon
```

**80-10-10 규칙** (15% 선택된 단어 중):

| 비율 | 처리 방식 | 이유 |
|------|----------|------|
| 80% | [MASK] 토큰으로 교체 | 주된 학습 신호 |
| 10% | 랜덤 단어로 교체 | 모든 위치의 표현을 강화 |
| 10% | 원래 단어 그대로 유지 | 파인튜닝 시 [MASK] 없는 상황 대비 |

> **왜 항상 [MASK]만 쓰지 않나?** 파인튜닝이나 실제 사용 시에는 [MASK] 토큰이 등장하지 않으므로, 사전학습과 실제 사용 간의 불일치를 줄이기 위해 80-10-10 규칙을 씁니다.

**학습 효율 트레이드오프**: MLM은 전체의 15%만 예측하므로 수렴이 약간 느립니다. 그러나 최종 성능은 단방향 LM보다 훨씬 높습니다.

### 사전학습 목적함수 2: NSP (Next Sentence Prediction)

**동기**: 자연어 추론, 질의응답 등 많은 NLP 태스크가 **두 문장 간의 관계** 이해를 필요로 합니다.

```
[CLS] the man went to [MASK] store [SEP] he bought a gallon [MASK] milk [SEP]
Label = IsNext   (50% 확률로 실제 연속 문장)

[CLS] the man [MASK] to the store [SEP] penguin [MASK] are flight##less birds [SEP]
Label = NotNext  (50% 확률로 랜덤 문장)
```

- `[CLS]`: 항상 맨 앞에 오는 특수 토큰 → NSP 분류에 사용
- `[SEP]`: 두 문장을 구분하는 특수 토큰

> **주의**: 후속 연구(RoBERTa, 2019)에서 NSP가 오히려 성능을 낮출 수 있다고 밝혀졌습니다.

### BERT 입력 임베딩

BERT의 입력은 세 가지 임베딩의 합입니다.

```
입력:  [CLS]  my   dog  is  cute  [SEP]  he  likes  play  ##ing  [SEP]
Token:  E_CLS  E_my E_dog E_is E_cute E_SEP E_he E_likes E_play E_##ing E_SEP
         +      +    +    +    +      +     +    +       +      +       +
Segment: E_A   E_A  E_A  E_A  E_A    E_A   E_B  E_B     E_B    E_B     E_B
         +      +    +    +    +      +     +    +       +      +       +
Position: E_0   E_1  E_2  E_3  E_4    E_5   E_6  E_7     E_8    E_9     E_10
```

- **Token Embedding**: 각 단어(wordpiece)의 의미 벡터
- **Segment Embedding**: 첫 번째 문장(A)인지 두 번째 문장(B)인지 구분
- **Position Embedding**: 단어의 위치 정보

### BERT 모델 규모

| 모델 | 레이어 수 | 히든 크기 | 어텐션 헤드 | 파라미터 수 |
|------|----------|----------|------------|------------|
| BERT-Base | 12 | 768 | 12 | 110M |
| BERT-Large | 24 | 1024 | 16 | 340M |

- 학습 데이터: Wikipedia(2.5B) + BooksCorpus(0.8B)
- 최대 입력 길이: 512 wordpiece 토큰
- 어휘 크기: 30,000 wordpieces

> **크기와 성능**: 모델이 클수록 성능이 더 높습니다 ("The bigger, the better!").

### BERT 파인튜닝: 다양한 태스크

BERT는 사전학습 후 최소한의 추가 파라미터만으로 다양한 태스크에 적용됩니다.

#### 문장 분류 (단일 문장)
```
[CLS] Tok1 Tok2 ... TokN
  ↓
C (CLS 토큰의 표현)
  ↓
Linear → 분류 레이블
```
추가 파라미터: `C × h`개 (C=클래스 수, h=히든 크기)

#### 문장 쌍 분류 (두 문장 관계)
```
[CLS] 문장1 [SEP] 문장2 [SEP] → C → Linear → 분류
```

#### 질의응답 (SQuAD)
```
[CLS] 질문 [SEP] 단락 [SEP]
→ 각 토큰에 대해 Start/End 위치 예측
```

#### 개체명 인식 (NER)
```
[CLS] Tok1 Tok2 ... TokN
→ 각 토큰에 대해 레이블 예측 (O, B-PER, ...)
```

### BERT가 학습하는 것: 어텐션 헤드 분석

BERT의 어텐션 헤드들은 자연스럽게 다양한 언어 패턴을 학습합니다.

| 헤드 | 학습 패턴 |
|------|----------|
| Head 1-1 | 문장 전체에 넓게 주목 |
| Head 3-1 | 다음 토큰에 주목 |
| Head 8-7 | [SEP] 토큰에 주목 |
| Head 11-6 | 마침표에 주목 |

---

## 7. GPT vs BERT 비교

| 항목 | GPT | BERT |
|------|-----|------|
| 기반 구조 | Transformer **디코더** | Transformer **인코더** |
| 방향성 | 단방향 (좌→우) | 양방향 |
| 사전학습 목표 | 다음 단어 예측 | MLM + NSP |
| 학습 데이터 | BooksCorpus | Wikipedia + BooksCorpus |
| 입력 길이 | 512 BPE 토큰 | 512 wordpiece 토큰 |
| 강점 | 텍스트 **생성** | 텍스트 **이해·분류** |
| 대표 활용 | ChatGPT, 대화형 AI | 구글 검색, 문서 분류 |
| 파인튜닝 방식 | 전체 파라미터 재학습 | 최소 파라미터 추가 |

### 실험 결과 (GLUE 벤치마크)

| 시스템 | 평균 점수 |
|--------|----------|
| Pre-OpenAI SOTA | 74.0 |
| BiLSTM + ELMo | 71.0 |
| OpenAI GPT | 75.1 |
| BERT-Base | 79.6 |
| **BERT-Large** | **82.1** |

### 어떤 모델이 어디에 쓰이나?

- **GPT 계열**: ChatGPT, Claude 등 대화형 AI — 텍스트를 생성해야 하는 서비스
- **BERT 계열**: 구글 검색, 스팸 필터, 감성분석, 의료 기록 분류 등 — 읽고 판단하는 서비스

---

## 8. Post-BERT 모델들

BERT 이후 연구들은 각기 다른 한계를 보완하며 발전했습니다.

### BERT의 주요 한계 4가지

| 한계 | 내용 |
|------|------|
| 학습 부족 | 데이터와 학습 시간이 충분하지 않았음 |
| 파라미터 과다 | 110M~340M으로 저장·배포 부담 |
| 학습 비효율 | MLM은 전체의 15% 토큰만 학습 신호를 받음 |
| 생성 불가 | 인코더 구조라 새로운 텍스트 생성 불가 |

---

### 8-1. RoBERTa (2019) — 학습 부족 보완

> Liu et al., 2019: *RoBERTa: A Robustly Optimized BERT Pretraining Approach*

**"BERT는 설계가 나쁜 게 아니라, 덜 학습된 것이다."**

#### 변경 사항

| 항목 | BERT | RoBERTa |
|------|------|---------|
| NSP | 사용 | **제거** (노이즈만 추가한다는 것이 밝혀짐) |
| 데이터 | 13GB | **160GB** (10배 이상) |
| 배치 크기 | 256 | **8,000** |
| 학습 스텝 | 1M | **500K** (더 오래) |
| GPU | - | 1,024 V100 × 1일 |

#### 결과

동일 구조에서 BERT-Large 대비 SQuAD, MNLI, SST-2 모두 큰 성능 향상.

---

### 8-2. ALBERT (2020) — 파라미터 과다 보완

> Lan et al., 2020: *ALBERT: A Lite BERT for Self-supervised Learning of Language Representations*

**"파라미터를 줄이면서 성능을 유지하자."**

#### 핵심 기법 2가지

**① 레이어 간 파라미터 공유**

BERT는 12개 레이어가 각각 다른 파라미터를 가집니다.  
ALBERT는 모든 레이어가 **같은 파라미터를 공유**합니다.

```
BERT:  레이어1(파라미터A) → 레이어2(파라미터B) → ...
ALBERT: 레이어1(파라미터X) → 레이어2(파라미터X) → ... (같은 파라미터 반복)
```

**② 임베딩 크기 분리 (Factorized Embedding)**

BERT: 임베딩 크기 = 히든 크기 (둘 다 768 또는 1024)  
ALBERT: 임베딩 크기(128) << 히든 크기(768~4096) → 분리하여 파라미터 절약

#### 규모 비교

| 모델 | 파라미터 | 레이어 | 히든 | 임베딩 | 속도 |
|------|---------|-------|------|--------|------|
| BERT-Base | 108M | 12 | 768 | 768 | 1.0x |
| BERT-Large | 334M | 24 | 1024 | 1024 | 1.0x |
| ALBERT-Base | **12M** | 12 | 768 | 128 | 5.6x 빠름 |
| ALBERT-xxLarge | 235M | 12 | 4096 | 128 | **0.3x 느림** |

> **주의**: ALBERT는 파라미터 수(저장 공간)는 줄었지만, 모델 구조 자체가 크면 추론 속도는 오히려 느릴 수 있습니다.

---

### 8-3. DistilBERT / TinyBERT / MobileBERT (2019) — 경량화

> Sanh et al., 2019: *DistilBERT: a distilled version of BERT — smaller, faster, cheaper and lighter*

**"큰 모델의 지식을 작은 모델에 전달하자." — 지식 증류(Knowledge Distillation)**

#### 지식 증류란?

```
교사(Teacher): BERT-Base (크고 정확함)
                   ↓ 출력 분포를 흉내 내도록 학습
학생(Student): DistilBERT (작고 빠름)
```

큰 모델의 최종 예측뿐 아니라 **중간 표현과 어텐션 패턴**까지 학습합니다.

#### DistilBERT 성능

| 항목 | BERT-Base | DistilBERT |
|------|----------|-----------|
| 파라미터 | 110M | 66M (**40% 감소**) |
| 추론 속도 | 1.0x | **1.6x 빠름** |
| GLUE 점수 | 79.5 | 77.0 (**97% 유지**) |

---

### 8-4. ELECTRA (2020) — 학습 비효율 보완

> Clark et al., 2020: *ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators*

**"15%만 학습하는 MLM은 낭비다. 100% 토큰을 모두 학습시키자."**

#### 구조: Generator + Discriminator

```
원본 문장: the chef cooked the meal

Step 1 — Generator (작은 MLM):
  the chef cooked the meal
          ↓ 일부 단어 마스킹 후 그럴듯한 단어로 교체
  the chef ate the meal  (cooked → ate)

Step 2 — Discriminator (ELECTRA):
  the  chef  ate  the  meal
   ↓     ↓    ↓    ↓    ↓
original original replaced original original
```

ELECTRA는 **모든 토큰**에 대해 "원래 단어인가, 교체된 단어인가"를 판별합니다.

#### BERT MLM vs ELECTRA 비교

| 항목 | BERT MLM | ELECTRA |
|------|----------|---------|
| 학습 신호 받는 토큰 | **15%** | **100%** |
| 학습 방식 | 마스크된 단어 예측 | 교체 여부 판별 |
| 파인튜닝 시 사용 | 인코더 전체 | **Discriminator만** (Generator 버림) |
| 효율성 | 낮음 | 높음 (같은 연산량에서 더 높은 성능) |

---

### 8-5. T5 (2020) — 생성 불가 보완

> Raffel et al., 2020: *Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer*

**"모든 NLP 태스크를 텍스트 입력 → 텍스트 출력으로 통일하자."**

#### 핵심 아이디어

```
번역:    "translate English to German: That is good."  → "Das ist gut."
문법:    "cola sentence: The course is jumping well."  → "not acceptable"
유사도:  "stsb sentence1: ... sentence2: ..."          → "3.8"
요약:    "summarize: state authorities dispatched ..."  → "six people hospitalized..."
```

어떤 태스크든 입력에 **태스크 설명을 prefix로 붙이고**, 출력을 텍스트로 받습니다.

#### 구조

```
인코더 (양방향) → 입력 문맥 깊이 이해
      ↓
디코더 (자동회귀) → 출력 텍스트 자유 생성
```

BERT의 양방향 이해력 + GPT의 생성 능력을 하나로 통합한 모델입니다.

#### T5 크기 종류

| 모델 | 파라미터 |
|------|---------|
| t5-small | 60M |
| t5-base | 220M |
| t5-large | 770M |
| t5-3b | 3B |
| t5-11b | 11B |

---

### Post-BERT 전체 비교 요약

| 모델 | 보완한 한계 | 핵심 기법 | 트레이드오프 |
|------|-----------|----------|------------|
| RoBERTa | 학습 부족 | 더 많은 데이터, NSP 제거 | 학습 비용 증가 |
| ALBERT | 파라미터 과다 | 파라미터 공유, 임베딩 분리 | 추론 속도 저하 가능 |
| DistilBERT | 파라미터 과다 | 지식 증류 | 성능 소폭 하락 (3%) |
| ELECTRA | 학습 비효율 | 판별자 방식 (100% 토큰) | 구조 복잡도 증가 |
| T5 | 생성 불가 | 인코더-디코더 통합 | 모델 크기 증가 |

---

## 9. 핵심 개념 한눈에 보기

### 사전학습 방식의 세 가지 유형

| 유형 | 구조 | 대표 모델 | 강점 |
|------|------|----------|------|
| Masked LM | Transformer **인코더** | BERT, RoBERTa, ALBERT, ELECTRA | 문장 이해·분류 |
| Autoregressive LM | Transformer **디코더** | GPT, GPT-2, GPT-3 | 텍스트 생성 |
| Text-to-Text | Transformer **인코더-디코더** | T5 | 이해 + 생성 통합 |

### 모델 발전 흐름

```
word2vec (정적 임베딩)
    ↓ 문맥 반영 필요
ELMo (BiLSTM 기반 동적 임베딩, feature-based)
    ↓ Transformer로 교체 + fine-tuning 방식
GPT (단방향, 생성 강점)  ←→  BERT (양방향, 이해 강점)
    ↓ BERT 한계 보완
RoBERTa / ALBERT / DistilBERT / ELECTRA / T5
```

### 실제 사용 예시

| 서비스/기능 | 사용 모델 계열 |
|-----------|--------------|
| ChatGPT, Claude | GPT 계열 (디코더) |
| 구글 검색 쿼리 이해 | BERT 계열 (인코더) |
| 스팸 필터 | BERT 계열 |
| 감성 분석 | BERT 계열 |
| 번역, 요약 | T5 계열 (인코더-디코더) |
| 모바일 NLP | DistilBERT, MobileBERT |

---

## 10. 시험 예상 문제 & 답

**Q1. word2vec과 ELMo의 가장 핵심적인 차이는?**

> word2vec은 단어 하나에 고정된 벡터 하나를 할당합니다 (정적 임베딩). ELMo는 같은 단어라도 문장 전체 문맥에 따라 매번 다른 벡터를 계산합니다 (동적 임베딩). ELMo의 모델 가중치는 훈련 후 고정되지만, 그 가중치로 계산되는 출력 벡터는 입력 문장마다 다릅니다.

**Q2. ELMo가 여러 층의 가중 평균을 사용하는 이유는?**

> 층마다 담는 언어 정보가 다르기 때문입니다. 낮은 층은 구문(문법) 정보를, 높은 층은 의미(semantics) 정보를 더 많이 담습니다. 태스크마다 필요한 정보가 다르므로, 가중치를 태스크에 맞게 학습합니다.

**Q3. BERT의 MLM에서 80-10-10 규칙을 사용하는 이유는?**

> 항상 [MASK]만 쓰면 파인튜닝이나 실제 추론 시에는 [MASK]가 없으므로 학습-사용 간 불일치(discrepancy)가 생깁니다. 10%는 랜덤 단어로, 10%는 원래 단어 그대로 두어 이 불일치를 줄입니다.

**Q4. GPT와 BERT의 구조적 차이와 이로 인한 활용 차이는?**

> GPT는 Transformer 디코더 기반 단방향 모델로 텍스트 생성에 강합니다 (ChatGPT 등). BERT는 Transformer 인코더 기반 양방향 모델로 문장 이해·분류에 강합니다 (검색, 감성분석 등). BERT는 텍스트를 새로 생성하는 것이 구조적으로 불가합니다.

**Q5. BERT의 NSP가 후속 연구에서 문제가 된 이유는?**

> RoBERTa 논문(2019)에서 NSP가 성능 향상보다 오히려 노이즈를 추가해 성능을 낮출 수 있다는 것이 실험적으로 밝혀졌습니다. NSP를 제거하고 더 많은 데이터로 더 오래 학습한 RoBERTa가 BERT를 큰 폭으로 앞질렀습니다.

**Q6. ELECTRA가 BERT MLM보다 효율적인 이유는?**

> BERT MLM은 전체 토큰의 15%만 마스킹하여 학습 신호를 받습니다. ELECTRA는 Generator가 단어를 교체하면 Discriminator가 모든 토큰(100%)에 대해 "원래인가, 교체됐는가"를 판별하므로, 같은 연산량에서 훨씬 더 많은 학습 신호를 받습니다.

**Q7. ALBERT가 파라미터를 줄이는 두 가지 방법은?**

> ① **레이어 간 파라미터 공유**: 모든 레이어가 동일한 파라미터를 공유합니다. ② **임베딩 크기 분리(Factorized Embedding)**: 단어 임베딩 차원을 히든 크기보다 훨씬 작게 유지합니다 (예: 128 vs 768). 단, 모델 구조 자체는 크기 때문에 추론 속도는 오히려 느릴 수 있습니다.

**Q8. T5가 GPT, BERT와 다른 점은?**

> T5는 인코더-디코더 구조로, 모든 NLP 태스크를 "텍스트 입력 → 텍스트 출력"으로 통일합니다. BERT(인코더만)는 생성이 불가하고, GPT(디코더만)는 단방향입니다. T5는 인코더의 양방향 이해력과 디코더의 자유로운 생성 능력을 동시에 갖춥니다.

**Q9. 다음 중 틀린 것은? (강의자료 퀴즈)**  
(a) BERT는 ELMo보다 더 많은 데이터로 학습되었다  
(b) BERT는 Transformer 인코더, GPT는 Transformer 디코더 기반이다  
(c) ELMo는 태스크마다 다른 모델 구조가 필요하다  
(d) BERT는 GPT에 비해 더 긴 문맥으로 학습되었다  

> **답: (d)** BERT와 GPT는 모두 512 토큰 단위로 학습됩니다. BERT가 더 긴 문맥으로 학습된 것은 아닙니다. (a)는 맞습니다. (b)는 맞습니다. (c)는 맞습니다 — ELMo는 feature-based이므로 각 태스크의 기존 모델에 임베딩을 추가하는 방식입니다.

---

*본 정리본은 강의자료(Lec09)와 수업 중 대화를 기반으로 작성되었습니다.*
