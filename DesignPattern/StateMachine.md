# 📝상태 머신

행동을 캡슐화하고 동적으로 변경할 수 있게 해줌  

## 기본 원리
```C#
public abstract class State
{
    Entity entity;

    public void Init(Entity entity)
    {
        this.entity = entity;
    }

    public override void Enter();
    public override void Update();
    public override void Exit();
}
```
```C#
public class Entity : MonoBehaviour
{
    State currentState;
    public void SetState(State state)
    {
        currentState?.Exit();

        currentState = state;
        currentState?.Enter();
    }

    void Update()
    {
        currentState?.Update();
    }
}
```
사용 시에는 아래와 같이 사용
```C#
// Entity 안에서
State idleState = new IdleState(this);  // IdleState는 State를 상속받은 구현체

// ...

SetState(idleState);
```

---

여러 상태의 자유로운 추가를 위해 아래와 같은 방법도 생각해볼 수 있음
```C#
public class StateMachine
{
    Dictionary<Type, State> states = new();
    State currentState;

    public void Register(State state)
    {
        states.Add(state.GetType(), state);
    }

    public void ChangeState<T>() where T : State
    {
        currentState?.Exit();

        currentState = states[typeof(T)];

        currentState.Enter();
    }

    public void Update()
    {
        currentState?.Update();
    }
}
```
```C#
public abstract class State
{
    protected Entity entity;
    protected StateMachine stateMachine;

    protected State(Entity entity, StateMachine stateMachine)
    {
        this.entity = entity;
        this.stateMachine = stateMachine;
    }

    public virtual void Enter() {}
    public virtual void Update() {}
    public virtual void Exit() {}
}


// 예시 State
public class IdleState : State
{
    public IdleState(Entity entity, StateMachine sm)
        : base(entity, sm) {}

    public override void Update()
    {
        if (entity.PlayerDetected())
        {
            stateMachine.ChangeState<MoveState1>();
        }
    }
}
```
```C#
[CreateAssetMenu(menuName="FSM/StateDB")]
public class StateDB : ScriptableObject
{
    [SerializeField] List<MonoScript> states;

    public List<MonoScript> States => states;
}
```
```C#
public class Entity : MonoBehaviour
{
    [SerializeField] StateDB stateDB;

    StateMachine stateMachine;

    void Awake()
    {
        stateMachine = new StateMachine();

        foreach (var script in stateDB.States)
        {
            var type = script.GetClass();
            var state = (State)Activator.CreateInstance(type, this, stateMachine);
            stateMachine.Register(state);
        }
    }

    void Start()
    {
        stateMachine.ChangeState<IdleState>();
    }

    void Update()
    {
        stateMachine.Update();
    }
}
```
- StateDB에서 해당 엔티티가 사용 가능한 State 스크립트들을 지정
- `stateMachine.ChangeState<TypeName>()`로 상태 변환

