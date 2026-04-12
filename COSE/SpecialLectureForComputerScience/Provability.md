# 🅿️오라클과 증명 가능성

## TQBF 오라클
TQBF(참인 양화 불리언 공식, True Quantified Boolean Formula) 문제를 한 번에 풀 수 있음

TQBF는 PSPACE에서 가장 어려운 문제 중 하나로, PSPACE 안의 모든 문제는 TQBF로 다항 시간 내에 변환(reduce) 될 수 있음  

<img width="642" height="372" alt="image" src="https://github.com/user-attachments/assets/0c5bfd4e-1480-48e3-9892-a468784132e9" />  
즉, TQBF 오라클이 있는 세계에서는 P = NP 가 됨

### P^TQBF = NP^TQBF 증명
NP^TQBF ⊆ P^TQBF 를 보이면 됨

#### NP^TQBF ⊆ NPSPACE
L ∈ NP^TQBF 인 언어가 주어졌을 때, 두 가지 경우가 있음:  
**1. L ∈ NP**  
NP ⊆ NPSPACE 이므로 자동으로 L ∈ NPSPACE

**2. TQBF를 결정하는 오라클 X를 사용하는 경우**  
X에 대한 질의(query)는 "어떤 언어가 TQBF에 속하는가?"를 묻는 것  
이것은 다항 공간을 사용하는 비결정론적 TM(nTM) 으로 시뮬레이션할 수 있음 (= NPSPACE 기계로 흉내 낼 수 있음)

따라서 두 경우 모두 L ∈ NPSPACE

#### NPSPACE = PSPACE
Savitch의 정리에 의해 성립

#### PSPACE ⊆ P^TQBF
TQBF는 PSPACE-complete => 즉, PSPACE 안의 모든 문제는 TQBF로 다항 시간 내에 변환(reduce)됨

따라서 TQBF 오라클이 있다면:  
임의의 PSPACE 문제를 TQBF로 다항시간 내에 변환하여 한 번에 해결 가능

==> PSPACE ⊆ P^TQBF

결론적으로 P^TQBF = NP^TQBF, 즉 이 오라클 세계에서는 P = NP

## 그래프 동형(Graph Isomorphism)은 NP에 속함
그래프 동형 문제: 두 그래프 G와 H가 있을 때, 노드 이름만 바꾸면 완전히 같은 그래프가 되는지를 묻는 문제

<img width="802" height="310" alt="image" src="https://github.com/user-attachments/assets/3f602410-4a75-43fe-a7aa-9a1730ee5f5c" />

### 왜 NP인지 증명
증명서(certificate) c = 이 동형 함수(permutation)가 주어졌을 때, 검증기 V(G, H, c)는:
1. c가 G의 노드들의 순열(permutation)인지 확인 → O(노드 수)
2. c에 따라 G의 노드를 재배치한 그래프가 H와 같은지 확인 → O(노드 수 + 엣지 수)

==> 두 단계 모두 다항 시간(polynomial time) 내에 수행 가능

## 인터랙티브 증명 시스템(Interactive Proof System)
집합 S에 대한 인터랙티브 증명 시스템은 **두 주체 사이의 게임**:
1. 검증자 V (Verifier): 확률적 다항 시간 전략 사용 (계산 능력 제한)
2. 증명자 P (Prover): 계산 능력 무제한

- 완전성(Completeness): x ∈ S 이면, 증명자 P와 상호작용 후 검증자 V는 반드시 accept 한다.
- 건전성(Soundness): x ∉ S 이면, 어떤 전략 P*를 써도 검증자 V는 적어도 1/p(|x|) 확률로 reject 한다.

이러한 인터랙티브 증명 시스템을 갖는 문제들의 집합 = ℐP (IP 클래스)

### 에러 감소
증명 시스템을 O(p(|x|)²)번 반복하면 V가 거짓 명제를 accept할 확률을 1 - 1/p(|x|) 에서 2^(-p(|x|)) 까지 낮출 수 있음

> 실용적으로는: 증명 시스템을 설계할 때는 NO인 경우에 눈에 띄는 reject 확률을 확보하는 것이 목표 (건전성 오류가 1보다 확실히 작으면 됨)
> 실제로 사용할 때는 건전성 오류가 무시할 수 있을 만큼 작다고 가정

### 그래프 비동형(Graph Nonisomorphism)의 인터랙티브 증명
- 그래프 동형 ∈ NP (앞서 증명)
- 그래프 비동형은 NP에 속하는지 알려지지 않음

> 비동형을 증명하려면?
> 증명자가 "이 두 그래프는 다르다"고 선언하는 것만으로는 검증자를 납득시킬 수 없음 (검증자는 증명자를 무조건 신뢰하지 않기 때문)

#### 프로토콜
1. 검증자가 G₁, G₂ 중 하나를 무작위로 선택하여 노드를 임의로 재배치한 그래프 H를 만든다.
2. 검증자가 H를 증명자에게 보낸다.
3. 증명자는 H가 G₁에서 왔는지 G₂에서 왔는지 선언해야 한다.

==>
- G₁ ≠ G₂ (실제로 비동형)일 때: 증명자는 H의 출처를 항상 정확히 식별 가능
- G₁ = G₂ (동형)일 때: H는 G₁이나 G₂ 어느 쪽에서도 올 수 있으므로, 아무리 강력한 증명자도 50:50 확률로만 맞힐 수 있음

==> **100번 반복해서 매번 맞힌다면**, 검증자는 두 그래프가 비동형이라는 강력한 증거를 얻게 됨

## 학습(Learning)과 가설(Hypothesis)

### 학습의 직관적 의미
- 관측(observation)을 설명하는 **가설(hypothesis)**을 세우는 과정 = 학습
- 새로운 관측이 추가되면 기존 가설을 수정하거나 새 가설로 교체
- 목표: 교사(teacher) 가 무작위로 제공하는 예시들을 보고, 그 예시들이 배워야 할 클래스에 속하는지 아닌지를 파악

<img width="533" height="287" alt="image" src="https://github.com/user-attachments/assets/9789d21b-5d00-4c38-add7-f4a3e7a0d81e" />

### Occam의 면도날과 좋은 가설
Occam의 면도날: 가능한 한 가장 단순한 가설 h를 선택하라
- "단순함"은 Kolmogorov 복잡도 의미에서 K(h)가 최소인 것

좋은 가설 h: 미래 관측에도 잘 맞아야 함 => `Pr[h(x) = f(x)] ≈ 1  (x는 P에 따라 무작위 선택)`  
핵심: 단순한 가설(Occam의 면도날 의미)을 찾으면, 그것이 높은 확률로 좋은 가설이기도 함

## 개념(Concept)과 개념 클래스(Concept class)
개념(concept): Boolean 함수 f: S → {0, 1}
- 샘플 공간의 각 원소를 0 또는 1로 분류
- 예: 어떤 문장이 문법적으로 맞는가(1) 아닌가(0)

개념 클래스 C: 가능하다고 생각하는 모든 개념들의 집합
- 예: 아기가 태어날 때 "가능한 언어들"의 집합 (데이터를 보기 전의 사전 가정)

샘플에 대한 확률 분포 D 를 가정

### 학습의 목표
<img width="741" height="270" alt="image" src="https://github.com/user-attachments/assets/9a50e9e3-7aff-4423-a15a-f17a280171f7" />

**실제 규칙 f와 근사하게 동작하는 가설 h를 찾기!**











