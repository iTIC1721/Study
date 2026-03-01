# 📝Delegate

메서드에 대한 참조 (함수의 주소값)

### 사용 방법

`접근제한자 delegate 반환타입 이름(매개변수)`  
`public delegate void MyDelegate(int param)`

```C#
// 예시
public void TestFunc(int num) {
    Debug.Log(num);
}


public delegate void MyDelegate();

void Start() {
    MyDelegate myDelegate;

    myDelegate = new MyDelegate(TestFunc);
    myDelegate(1);    // myDelegate 호출 시 TestFunc 실행

    // 축약형
    myDelegate = TestFunc;
    myDelegate.Invoke(1);    // Invoke로 호출해도 동일하게 동작 (myDelegate?.Invoke()처럼 null 방지 등으로 사용)
}
```

### 사용 이유
1. 함수를 매개변수나 반환 형식으로 사용
2. 콜백
```C#
// 예시
public enum Buff {
    None,
    Buff1,
    Buff2
}

private delegate void BuffDelegate();    // 타입 정의

private BuffDelegate _buffDelegate;    // 객체 선언

private Buff _buff;
public Buff _Buff {
    get { return _buff; }
    set {
        if (_buff == value) return;

        _buff = value;
        switch (_buff) {
            case Buff.None:
            _buffDelegate = BuffNone;
            break;

            case Buff.Buff1:
            _buffDelegate = Buff1;
            break;

            case Buff.Buff2:
            _buffDelegate = Buff2;
            break;
        }
    }
}

public void Attack() {
    _buffDelegate();
    Debug.Log("Attack");
}

void BuffNone() {}
void Buff1() { Debug.Log("Buff1"); }
void Buff2() { Debug.Log("Buff2"); }
```

→ 델리게이트를 사용하면 Attack이 실행되지 않더라도 델리게이트가 이미 조건에 맞는 함수를 참조하므로, 코드 의존성이 줄어듦


## 델리게이트 체인

여러 델리게이트가 순서대로 실행되도록 동시에 여러 함수 참조

1. `Delegate.Combine()` 사용
2. \+ 연산자 사용
3. += 연산자 사용

```C#
// 예시
private delegate void MyDelegate();
private MyDelegate myDelegate;

void Chain1() {}
void Chain2() {}
void Chain3() {}

void Start() {
    myDelegate = Chain1;
    myDelegate += Chain2;
    myDelegate += Chain3;

    myDelegate.Invoke();
}
```

## 델리게이트 이벤트

객체의 **상태 변화**나 **사건의 발생**을 ***알리는 용도***로 사용

```C#
public delegate void MyDelegate();
public event MyDelegate myDelegate;
```

### 특징

1. 외부에서 호출할 수 없음 (외부에서 체인을 추가할 수는 있지만, 외부에서 실행하면 안될 때 사용)
2. 외부에서 대입 연산자(=)를 사용할 수 없음 (외부에서 델리게이트의 체인을 끊고 초기화하는 것을 막아줌)
→ 안정적인 이벤트 기반 프로그래밍 가능


#### 정리
델리게이트는 **콜백 기능**이 필요할 때 사용  
이벤트는 객체의 상태 변화나 사건 발생을 **알리는 용도**로 사용

