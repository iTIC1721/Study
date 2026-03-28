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
어떤 구조 A에 대해 "존재하는 어떤 2차 논리식(s)"이 참이면 NP에 포함

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

## Heuristics

NP-hard 문제(SAT 등)는 항상 빠르게 풀 수는 없음  
대신 "잘 되는 경우가 많게" 만드는 전략 => **heuristic**

### 탐색 전략
SAT는 본질적으로 경우의 수가 2^n이므로 완전 탐색하면 너무 느림  
=> 탐색 전략이 필요

1. **Optimist**

해가 존재할 것이라는 관점 -> 될 것 같은 방향으로 밀어붙임
- 변수 하나를 선택하여 일단 true를 넣어봄
- 식이 깨지지 않는다면 그 부분 제거

2. **Pessimist**

해가 존재할 수 없을 것이라는 관점
- false를 넣어가며 모든 경우를 논리적으로 고려하여 모든 경우에 불가능하다는 것을 확인

### Backtracking
Optimist와 Pessimist를 결합하여 진행

Optimist처럼 진행하다가, 실패하면 Pessimist 방향으로 수행
```
문제
(p ∨ ~q) ∧ (q ∨ r) ∧ (~r ∨ ~p)

풀이
1. 변수 p를 선택 후 true를 넣어봄
2. (p ∨ ~q) = true    => true이므로 이 부분은 지움
3. p가 true이므로 (~r ∨ ~p)에서 ~p는 지워짐    => (~r)
4. 변수 q를 선택 후 true를 넣어봄
5. (q ∨ r) = true    => true이므로 이 부분도 지움
6. 변수 r을 선택 후 true를 넣어봄
7. (~r) = false이므로 모순 발생!!
8. 가장 최근 결정으로 backtrack    => r에 false 넣어봄
9. (~r) = true    => true이므로 이 부분도 지움
10. 전체 formula가 지워짐 => 해 찾기 성공
```

## Boolean Circuit
계산을 표현할 수 있는 또다른 계산 모델

Boolean Circuit = 방향성 비순환 그래프 (DAG)
- input node: 입력 변수
- gate (non-input node): AND / OR / NOT
- output node: 최종 결과

ex) 3-bit full adder

회로의 크기(size): 회로에 있는 게이트의 총 개수 (input과 output 제외)  
회로의 깊이(depth): 입력에서 출력까지 이동하는 가장 긴 경로의 길이 (input과 output 제외)

### Circuit Family
기존 Circuit은 입력 크기가 고정되어 있음 -> 길이가 변하는 입력은 대응 불가

=> 입력 크기 n마다 별도의 회로 Cn 존재  
        => 이것이 **Circuit Family**

## Hardware vs Software
같은 문제를 하드웨어(Boolean Circuit 등)으로도 풀 수 있고, 소프트웨어(Universal TM, 튜링 완전 프로그래밍 언어 등)로도 풀 수 있음

이론적으로는 Hardware = Software  
but 실제로는 약간의 차이 있음

1. Efficiency: 소프트웨어는 순차적으로 실행하지만 하드웨어는 병렬적으로 실행할 수 있음
2. Circuit은 입력 길이마다 다른 회로를 사용할 수 있지만, 소프트웨어는 하나의 프로그램으로 모든 입력 처리

## Quine
자기 자신을 출력하는 프로그램

von Neumann 구조에서는 **프로그램 = 데이터**  
코드가 메모리에 저장되므로, 프로그램이 자기 자신을 읽을 수 있음

## Rice Theorem
> 프로그램의 의미적 성질은 일반적으로 판별 불가능하다

F를 모든 계산 가능한 부분 함수들 집합의 비자명한 부분집합이라고 하자.  
그리고 Sf를 F에 속하는 함수를 계산하는 기계들을 기술하는 문자열 집합이라고 하자.  
=> **Sf에 속하는지 여부를 결정하는 것**은 알고리즘으로 해결할 수 없음

이유: 프로그램은 자기 참조가 가능(quine 등), Halting Problem과 같은 이유.

## Uniform Circuit Family
모든 입력 길이 n에 대해서 Circuit Cn을 만드는 것이 쉽지 않을 수 있음  
n이 작다고 항상 만들기 쉬운 것은 아니며, 오히려 n이 클수록 만들기 쉬울 수도 있음  

Circuit Family가 uniform하다는 것은: 모든 n에 대해 Cn을 O(log s(n)) 안에 만들어낼 수 있어야 함  
※ s(n)은 회로 Cn의 크기

## Simulation of t(n) time bound TM / s(n) space bound TM

t(n) time bound TM은 크기 O( t(n)logt(n) )의 uniform circuit으로 시뮬레이션될 수 있음  
s(n) space bound TM은 깊이 O( s*(n)^2 )의 uniform circuit으로 시뮬레이션될 수 있음 ( s*(n) = max{s(n), upper(logn)} )

## Polynomial Circuits

어떤 언어 L이 polynomial circuits를 가진다는 것은

- 입력 길이 n마다 회로 Cₙ이 있고
- 그 회로 크기는 polynomial이고
- 그 회로가 L의 정답을 정확히 계산한다는 뜻

### Theorem
1. P 문제는 전부 polynomial 크기의 회로로 구현 가능
2. 확률 알고리즘(BPP)도 polynomial 회로로 표현 가능
3. L이 uniform polynomial circuits를 가짐 **iff** L이 P에 속함


## 2 OPEN PROBLEMS
1. P 문제들, 또는 NP 문제들이 전부 O(n) 크기(선형 크기) 회로로 가능한지?
2. 반드시 exponential-size(2^n) 회로가 필요한 문제가 존재하는가?

둘 다 아직 미해결



























