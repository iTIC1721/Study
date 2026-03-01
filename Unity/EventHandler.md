# 📌EventHandler

`using System;` 선언 후 사용 가능  
내부 정의: `public delegate void EventHandler(object sender, EventArgs e);`
- `object sender`: 이벤트를 실행시키는 객체
- `EventArgs e`: 전달하고 싶은 정보 (없을 경우 EventArgs.Empty 사용)

`public event EventHandler eventHandler;` → 델리게이트 타입 선언 없이 바로 사용 가능

## EventArgs
```C#
// EventArgs 정의
namespace System {
    public class EventArgs {
        public static readonly EventArgs Empty;

        public EventArgs();
    }
}
```

전달하고 싶은 정보가 있을 때, EventArgs를 상속받은 클래스로 만들어 전달  
```C#
// 예시
public class EventTest : EventArgs {
    public string _name;

    public EventTest(string name) {
        _name = name;
    }
}
```
```C#
// 사용 예시
public event EventHandler eventHandler;

void Start() {
    EventTest eventTest = new EventTest("TestEventName");

    eventHandler += Test;
    eventHandler.Invoke(this, (EventArgs)eventTest);    // 형변환 해야함

}

void Test(object o, EventArgs e) {
    Debug.Log("test");
    Debug.Log(((EventTest)e)._name);    // 형변환 해야함
}
```

## 제네릭을 이용한 EventHandler
형변환이 필요없는 형태의 EventHandler  
내부 정의: `public delegate void EventHandler<TEventArgs>(object sender, TEventArgs e);`

사용법은 위와 동일하지만, 객체 선언 시 `public event EventHandler<EventTest> eventHandler;`로 선언하면 EventArgs의 형변환 없이 사용 가능

## EventHandler의 다른 형태
프로퍼티와 비슷하게, 구독자 등록 및 해지 시의 이벤트를 커스터마이징할 수 있음 (중복 구독 방지, 로그 기록 등에 사용)

```C#
private EventHandler _eventHandler;
public event EventHandler eventHandler {
    add {
        Debug.Log("ADD");
        _eventHandler += value;
    }
    remove {
        Debug.Log("REMOVE");
        _eventHandler -= value;
    }
}

void Start() {
    _eventHandler.Invoke(this, EventArgs.Empty);
}
```
