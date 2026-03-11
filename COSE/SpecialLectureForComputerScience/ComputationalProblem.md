# 🅿️계산 문제 기초

## 계산 문제의 정의

계산 문제는 **Input**과 **Output** 두 요소로 정의됨  
또한, 각 문제의 입력을 Instance라고 함

### Traveling Salesman Problem (TSP)

**Input**: 
- 그래프 G = (V, E)
- 각 도시 사이의 이동 비용
- 기준 비용 B

**Output**:  
기준 비용 B를 넘기지 않고 모든 도시를 한 번씩 방문하고 돌아오는 경로가 존재하는가?  
- Yes / No

#### TSP의 계산량
도시의 개수가 n개인 경우 가능한 경로 수: `n! = n x (n - 1) x (n - 2) x ... x 1`

Brute Force 방식으로 계산하기 때문에 n이 커지면 계산량이 기하급수적으로 증가

### Partition Problem

**Input**: 
- 양의 정수 집합 A
- 정수 x

**Output**:  
A의 부분집합의 합이 x인 경우가 존재하는가?   
- Yes / No

ex) A = {1, 4, 10, 5, 9, 12, 3}, x = 7  
    => 가능한 부분집합: {4, 3}이 존재하므로 Yes  

ex) A = {1, 2, 3, 4, 10}, x = 100  
    => 가능한 부분집합이 없음: No

### 효율적 알고리즘이란?
▶ 시간복잡도가 Polynomial Time인 알고리즘 → O(n^k)  
(Cobham-Edmonds Thesis: 실제적으로 해결 가능한(feasibly computable) 문제란 다항 시간(Polynomial Time, P) 내에 풀 수 있는 문제를 의미한다)

### 중요한 사실
1. 계산 문제는 무한히 많이 존재
2. 알고리즘은 유한히 존재
3. 따라서, computable하지 않은 문제 또한 존재함


## NP 문제

TSP와 Partition Problem의 공통점)  
▶ 가능한 해 공간을 탐색해서 답을 찾아야 함 → Search Problem

**P 문제**: Polynomial Time에 해결 가능한 문제 (efficient algorithm이 존재)  
**NP 문제**: 해가 맞는지 확인하는 것이 Polynomial Time으로 가능 → 해를 찾기는 어렵지만 해가 맞는지 확인은 쉬움

`P ⊆ NP`임은 알려져 있음  
하지만 `NP ⊆ P`인지는 아직 모름


### Shortest Path 문제의 성질

ex) 시카고 → 뉴욕의 경로가 피츠부르크를 거친다면, 전체 최단 경로 = (시카고 → 피츠부르크)의 최단경로 + (피츠부르크 → 뉴욕)의 최단경로

모든 경우를 탐색하지 않아도 해결 가능 ▶ **Optimal Substructure**  
(Dynamic Programming)

### k-Clique 문제

**Input**: 
- 그래프 G = (V, E)
- 정수 k ≥ 2

**Output**:  
k개의 노드가 모두 서로에게 연결된 구조(clique)가 존재하는가?  
- Yes / No

### 자유 과제
```
Reading assignment – submission is NOT required (I will explain parts of these in class)

Download the paper 

“The status of the P versus NP problem” by Lance Fortnow, and

(available up at http://people.cs.uchicago.edu/~fortnow/papers/pnp-cacm.pdf  )

Read (1) section 2, what is the P versus NP problem?, and
       (2) section 3 What if P = NP?
<img width="1539" height="486" alt="image" src="https://github.com/user-attachments/assets/07593c13-e2ce-4ad6-95fc-afe468bad469" />

```

## 알고리즘이란?

### 튜링 머신 (TM)
단순화한 계산 모델

**구성 요소**
1. Tape: 무한한 길이의 1차원 메모리, 각 칸에 Symbol들을 저장함  
`... ㅁ ㅁ 0 1 1 0 0 ㅁ ㅁ ...`

3. Tape Head: 테이프 위를 움직이는 읽기/쓰기 장치

4. State: TM의 현재 상태
```
q0 (initial state)
q1
q2
q_accept
q_reject
```

5. Transition Functions: 튜링 머신의 프로그램, 현재 상태 + 읽은 심볼로 행동을 결정
  - δ(q, a) = (q', b, D)
    - 현재 상태 q
    - 읽은 심볼 a
    - 다음 상태 q'
    - 쓸 심볼 b
    - 이동 방향 D (왼쪽 / 오른쪽)

### 튜링 머신의 7-tuple 구조
튜링 머신은 아래 7개의 요소로 구성됨

`M = (Q, Σ, Γ, δ, q0​, q_accept​, q_reject​)`

1. Q : 상태 집합
2. Σ : 입력 문자 집합
3. Γ : 테이프 문자 집합
4. δ : Transfer Function
5. q0 : Initial State (초기 상태)
6. q_accept​ : Accept State
7. q_reject : Reject State

### Configuration
튜링 머신의 현재 상황, 다음 세 가지를 포함함  
1. tape contents
2. head 위치
3. 현재 state

ex)  
`01q100`
```
  q
  ↓
01100
```

### 튜링 머신 계산 과정
configuration sequence를 만들어 계산함

ex)
`q0 101` → `1 q1 01` → `10 q2 1` → `accept`

### Partial Function
튜링 프로그램은 모든 상태.심볼이 정의되지 않을 수 있기 때문에 Partial Function임

ex) `δ(q0, 1)`만 정의되어 있는 튜링 프로그램에 `δ(q0, 0)`를 실행시키면 프로그램은 멈춤 (halt)

### Universal TM
다른 튜링 머신을 시뮬레이션할 수 있는 튜링 머신

입력:  
<Machine M, Input w>

출력:  
M(w) 실행

▶ Universal TM 아이디어에서 디자인된 **폰 노이만 구조** 하에서, 모든 program은 string과 같음


### Nondeterministic TM (nTM)
Transition Function의 결과가 하나로 정해지지 않고 여러 선택이 가능함

ex)
```
δ(q0, 1) =
(q1, 0, R)
(q2, 1, L)
```

Deterministic TM과 같은 언어를 인식하기에 계산 능력은 같음 but 시간 효율성은 다를 수 있음!


## Language
### Language accepted by TM
문자열 집합 L에 대해서  
입력 w ∈ L이면 **TM이 accept state에 도달**

### Language decided by TM
문자열 집합 L에 대해서  
입력 w ∈ L이면 **TM이 accept state에 도달**  
입력 w ∉ L이면 **TM이 reject state에 도달**  
▶ 이 경우 TM은 Decider라고 부를 수 있음


## 시간 복잡도

### Asymptotic Notation
- O : worst case
- Ω : best case
- Θ : average

### PATH Problem
그래프에서 s → t 경로의 존재 여부

#### BFS 알고리즘
```
// Yes Instance
s marked
↓
(s, a), (a, b), (a, t), (b, s), (b, t): mark a
↓
(a, b), (a, t), (b, s), (b, t): mark b, t
↓
t가 mark되었으니 accept!
```
```
// No Instance
s marked
↓
(s, a), (a, b), (b, s), (t, a), (t, b): mark a
↓
(a, b), (b, s), (t, a), (t, b): mark b
↓
t가 mark될 수 없으니 reject
```

시간복잡도: O(|V| + |E|)  
따라서 PATH 문제는 P Problem

### 공간 복잡도에 대해서는?
PSpace = NSpace  
큰 차이 없음


## Verifier
어떤 문제 L에 대해 (문제 x, 증거(=certificate) y)를 입력받아서 y가 올바른 증거인지 빠르게 검사하는 알고리즘
```
L ∈ NP 
iff
polynomial time verifier가 존재
```


## NP 문제의 대표적인 예시

### Satisfiability Problem (SAT)
주어진 Boolean formula φ가 있을 때,  
변수값을 적절히 선택해서 φ = TRUE가 될 수 있는가?

ex)
```
φ = (x ∨ y) ∧ (¬x ∨ z)
(x=1, y=0, z=1) ==> TRUE
```

### TQBF Problem
Quantifier: ∀, ∃  
- ∀ : for all = 모든
- ∃ : there exists = 존재한다

ex) `ϕ=∀x∃y[(x∨y)∧(xˉ∨yˉ​)]`  
==> 모든 x 값에 대해서, 어떤 y 값을 잘 고를 수 있어서,  
(x∨y)∧(xˉ∨yˉ​)가 참이 되는가?

fully quantified: 모든 변수들이 Quantifier에 의해 묶여 있음 → 참/거짓이 정해질 수 있음

TQBF problem: 입력으로 fully quantified Boolean formula 가 주어졌을 때, 이 식이 참인가?

#### SAT와 비교
SAT는 TQBP의 특수한 형태

### 중요한 정리
만약 SAT ∈ P이면 NP ⊆ P이게 되므로 P = NP가 됨














