# 🅿️P - NP

Remind: 만약 SAT 문제(NP-Complete)를 빠르게 풀 수 있다면, 모든 NP 문제도 빠르게 해결 가능

SAT는 원래 "해가 존재하는지"를 찾는 문제(**decision problem**)지만, 실제로 중요한 것은 "해를 찾을 수 있는가"(**certificate 찾기**) << 어떻게?

### 해결 아이디어
1. SAT를 다항시간 내에 결정하는 dTM M이 있다고 가정
2. M을 통해 formula f가 SAT인지 확인
3. f를 두 개의 sub-formula로 분해 (OR로 결합된 f = f1 ∨ f2)
4. 둘 중 SAT인 쪽 선택 → 반복하여 변수의 값을 하나씩 결정

▶ 변수가 n개라면 최대 2n + 1번의 M 호출로 해결 가능  
M은 다항시간으로 해결 가능하므로 전체도 다항시간으로 해결 가능

따라서 **SAT ∈ P라면 certificate(해)도 다항시간 내에 찾을 수 있음**

## Oracle

oracle: 어떤 언어 A에 대해 x ∈ A인지(membership query) 즉시 답해주는 블랙박스 (시간복잡도를 무시하는 가상의 도구)  
Oracle Turing Machine: 오라클을 호출 가능한 TM

어떤 오라클을 선택하느냐에 따라 문제의 분류 구조가 바뀜 ==> different world  
어떤 오라클 A에서는 P^A = NP^A  
어떤 오라클 B에서는 여전히 P^B = NP^B  

따라서 오라클을 이용한 증명은 P vs NP를 결정할 수 없음

## Promise Problem

### Vertex Cover
그래프 G = (V, E)에서 V(정점)의 subset C가 그래프의 모든 간선을 "덮으면" Vertex Cover라고 함  
덮는다: 모든 간선의 양 끝 정점 중 하나 이상이 C에 속함

**Vertex Cover 문제**: 크기 ≤ k인 vertex cover가 존재하는가? => NP-Complete

### Promise Problem: 새로운 유형의 문제
3가지 요소로 구성: (Instance, Promise, Question)
1. Instance: 입력
2. Promise: 입력이 특정 조건을 만족한다고 가정
3. Question: 그 조건 하에서 YES/NO 판단

```
예시)
입력: 그래프 G
promise: G는 반드시 bipartite 또는 non-bipartite
질문: bipartite인가?

==> Promise가 없으면 문제 정의가 애매해짐
```

**Decision Problem ⊆ Promise Problem**  
결정 문제는 Promise Problem의 특수한 경우  
(결정 문제는 모든 입력 허용, Promise Problem은 특정 입력만 허용)

### BPP (Bounded error probabilistic polynomial time)
오류 확률이 제한된 확률적 다항시간 모델

언어 L이 BPP에 속한다  
⇔  
확률적 튜링 머신 M이 존재해서:  

- x ∈ L ⇒ P[M(x) = 1] ≥ 2/3    ​
- x ¬∈ L ⇒ P[M(x) = 0] ≥ 2/3    

BPP 알고리즘은 항상 맞지는 않지만 **높은 확률로 맞음​**  
같은 알고리즘을 n번 반복하면 오류 확률을 (1/3)^n으로 줄일 수 있음

### PTM (Probabilistic Turing Machine)
각 단계에서 두 개의 가능한 전이 함수 중 하나가 랜덤하게 선택되는 계산 모델 (계산 과정 자체가 확률적)  
BPP는 PTM 기반으로 계산

전이 함수:
```
δ(q,x) =
    δ1​(q,x)    확률 1/2
    δ2​(q,x)​    확률 1/2​​
```

### P 문제와의 관계
P ⊆ BPP (P[correct] = 1인 특수한 경우)

하지만 P = BPP인지는 아직 모름

## Truth-table Reduction
A가 B로 truth-table reducible 하다 

⇔

1. 계산 가능한 함수 f가 존재하고
2. f(n)은 B에 대한 유한 개의 membership 질문으로 이루어진 Boolean 질의이며
3. n ∈ A⟺B가 f(n)을 만족한다

**여러 개 질문을 “한 번에” 던지고, 그 답들을 조합해서 문제를 푸는 방식**

```
예시)
풀기 어려운 문제 A: “x가 A에 속하는가?”

풀 수 있는 문제 B가 있음
-> B에게 여러 질문 q1, q2, q3을 던짐
    q1 = x
    q2 = reverse(x)
    q3 = ...
-> 그 결과를 조합: B(q1​)∧(B(q2​)∨¬B(q3​))
-> 이것으로 A의 결과를 결정

==> A는 B로 truth-table reducible하다
```

핵심 특징
1. non-adaptive (비적응형): 모든 쿼리는 미리 결정됨
2. parallel query: 쿼리를 동시에 수행
3. Boolean 조합

## P-Selective
언어 L이 P-selective 

⇔

다항시간 함수 f(x,y)가 존재해서:

- f(x,y) ∈ {x,y}
- if x∈L **or** y∈L, then f(x,y) ∈ L

**적어도 하나가 정답이면 정답 쪽을 골라준다** => 약한 형태의 결정 능력

### P=NP이면 모든 P 언어가 P-selective

P=NP이면 NP 문제를 다 polynomial에 해결 가능

## Binary Decision Tree
**불리언 함수**를 나타내는 이진 결정 트리

- 각 내부 노드는 함수의 변수를 나타낸다
- 각 내부 노드는 두 개의 자식을 가진다: 왼쪽 = True, 오른쪽 = False
- 각 leaf는 함수(경로)의 결과값을 나타낸다

하나의 불리언 함수에 대해 여러 개의 서로 다른 결정 트리가 존재할 수 있음

### 두 개의 결정 트리가 같은 함수를 나타내는지 확인하는 문제
입력:  
Binary Decision Tree A  
Binary Decision Tree B

출력:  
모든 입력에 대해 같은 결과를 내는지

==> coNP 문제 (답이 아닌지 검증하기 쉬운 문제)  
∵ 반례 하나만 찾으면 됨

이 문제가 P에 속하는지는 알려져 있지 않음  
but 확률적 튜링 머신으로 해결 가능 -> BPP에는 속함  
- 해결 방법: 여러 번 랜덤 테스트 -> 다르면 바로 발견됨

## LI vs LP

LI (Linear Inequalities)
- 주어진 연립일차부등식을 만족하는 변수집합이 있는지
```
​x1 ​+ 2x2 ≤ 5
4x1​ + x2 ≤ 8
x1​ + 5x2 ≤ 10​

이 조건을 만족하는 x1, x2가 존재하는가?
```

LP (Linear Programming)
- 같은 제약조건에 목적 함수가 있음

```
목적함수: maximize c1x1 + c2x2​
==> “조건을 만족하면서 최대값은 얼마인가?”
```

둘은 polynomial-time 서로 변환 가능 (계산 복잡도 동일)  
LI → LP: 목적함수가 없는/상관없는 LP로 변경 가능  
LP → LI: 최적해는 항상 **꼭짓점(vertex)**에서 발생하므로, feasible region만 알면 경계 분석으로 해결 가능

## Descriptive Complexity

기존 (Algorithm 관점): P, NP 정의 → Turing Machine 기반  
새로운 (Logic 관점): **문제를 논리식으로 정의하자**

### NP의 새로운 정의
NP = Existential Second-Order Logic (SOE)
> ∃R such that ϕ(A,R) = true
> A: 입력 구조 (그래프 등)
> R: 우리가 찾는 **증명** (certificate)

```
예시: 3-Coloring
입력: 그래프 G=(V,E)
조건:
    ∃c : V → {1,2,3}
    ∀(u,v)∈E, c(u) ≠ c(v)

==> 색칠 방법이 존재하는가?
```
```
예시: Hamiltonian Cycle
    ∃(cycle C) such that 모든 정점을 정확히 한 번 방문
```

























