# 📝상태 머신

행동을 캡슐화하고 동적으로 변경할 수 있게 해줌  

## 사용 방법
```C#
public abstract class State
{
    Entity entity;

    public void Init(Entity entity)
    {
        this.entity = entity;
    }

    public override void OnStateEnter();
    public override void OnStateUpdate();
    public override void OnStateExit();
}
```
```C#
public class Entity : MonoBehaviour
{
    State currentState;
    public void SetState(State state)
    {
        currentState?.OnStageExit();

        currentState = state;
        currentState?.OnStateEnter();
    }

    void Update()
    {
        currentState?.OnStateUpdate();
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

여러 상태의 자유로운 추가를 위해 Factory 패턴을 활용해 아래와 같은 방법도 생각해볼 수 있음
```C#
public static class StateFactory
{
    private static Dictionary<string, Func<Entity, State>> creators
        = new();

    public static void Register(string name, Func<Entity, State> creator)
    {
        creators.Add(name, creator);
    }

    public static State Create(string name, Entity entity)
    {
        if (creators.TryGetValue(name, out var creator))
            return creator(entity);

        throw new Exception($"State {name} not registered");
    }
}
```
```C#
public class IdleState : State
{
    // 자동으로 이름 등록
    static IdleState()
    {
        StateFactory.Register("Idle", e => new IdleState(e));
    }

    public IdleState(Entity entity) : base(entity) {}

    public override void Enter()
    {
        Debug.Log("Idle");
    }
}
```
```C#
[CreateAssetMenu(menuName="ScriptableObject/StateDB")]
public class StateDB : ScriptableObject
{
    [SerializeField] List<string> states;

    public List<string> States => states;
}
```
```C#
public class Entity : MonoBehaviour
{
    [SerializeField] StateDB stateDB;
    Dictionary<string, State> states = new();

    State currentState;

    void Awake()
    {
        foreach (var name in stateDB.States)
        {
            states.Add(name, StateFactory.Create(name, this));
        }
    }

    public void SetState(State state)
    {
        currentState?.OnStageExit();

        currentState = state;
        currentState?.OnStateEnter();
    }

    void Update()
    {
        currentState?.OnStateUpdate();
    }
}
```

