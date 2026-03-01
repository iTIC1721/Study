# 📌Action / Func

###### 참조
[델리게이트](./Delegate.md)  
[람다식](./Lambda.md)

---

델리게이트 선언의 번거로움을 줄이기 위해 미리 자체적으로 선언된 델리게이트  
`using System;` 선언을 통해 사용 가능

## Action

**반환타입이 없는** 델리게이트  
`public delegate void Action();`

선언할 필요 없이 바로 사용 가능
```C#
// 예시
Action action;

void Start() {
    action = TestAction;                   // 일반 델리게이트처럼 사용 가능
    action += () => Debug.Log("Test2");    // 람다식도 사용 가능

    action.Invoke();
}

void TestAction() {
    Debug.Log("Test");
}
```

제네릭을 이용해 최대 16개의 매개변수를 입력받을 수 있음
```C#
// 예시
Action<int> action1;
Action<int, int> action2;
Action<string, string, string> action3;

void Start() {
    action1 = Test1;
    action2 = Test2;
    action3 = Test3;

    action1.Invoke(1);
    action2.Invoke(1, 2);
    action3.Invoke("a", "b", "c");
}

void Test1(int num) { Debug.Log(num); }
void Test2(int num1, int num2) { Debug.Log(num1 + num2); }
void Test1(string s1, string s2, string s3) { Debug.Log(s1 + s2 + s3); }
```

## Func

**반환타입이 있는** 델리게이트  
`public delegate TResult Func<out TResult>();`

선언할 필요 없이 바로 사용 가능
```C#
// 예시
Func<int> func;

void Start() {
    func = TestFunc;                      // 일반 델리게이트처럼 사용 가능
    func += () => {
        Debug.Log("Test2");
        return 2;
    };                                    // 람다식도 사용 가능

    int result = func.Invoke();           // 최종 반환값은 2 (체인의 마지막 반환값 반환)
}

void TestFunc() {
    Debug.Log("Test1");
    return 1;
}
```

제네릭을 이용해 최대 16개의 매개변수를 입력받을 수 있음  
제네릭의 마지막 타입이 반환값의 타입(<T1, T2, ..., T16, TResult>)
```C#
// 예시
Func<int, int> func1;
Func<int, int, int> func2;
Func<string, string, string, string> func3;

void Start() {
    func1 = Test1;
    func2 = Test2;
    func3 = Test3;

    int result1 = func1.Invoke(1);
    int result2 = func2.Invoke(1, 2);
    string result3 = func3.Invoke("a", "b", "c");
}

void Test1(int num) { return num; }
void Test2(int num1, int num2) { return num1 + num2; }
void Test1(string s1, string s2, string s3) { return s1 + s2 + s3; }
```

## UnityAction

유니티에서 자체적으로 제공하는 Action  
`using UnityEngine.Event;` 선언을 통해 사용 가능

Action과 동일하게 작동하지만, 매개변수는 최대 4개까지 입력 가능

### Action vs UnityAction
- UnityAction은 Action보다 최소 2배 이상 느림
- Event Listener(해당 이벤트를 구독한 객체/메서드)가 2개 이상인 경우 UnityAction이 메모리를 덜 Allocate함 → GC가 덜 발생함
- Event Dispatch(Invoke 호출) 시 Action은 가비지를 발생시키지 않지만, UnityEvent는 처음 호출할 때 가비지가 발생함

+) UnityEvent는 UnityAction을 매개변수로 받음
