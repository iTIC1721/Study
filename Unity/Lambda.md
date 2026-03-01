# 📌람다식

## 익명 메서드

하나의 델리게이트 변수에서만 사용하는 함수는 굳이 델리게이트용 함수를 따로 만들지 않고 익명 메서드로 간단하게 작성할 수 있음

```C#
// 예시
delegate void MyDelegate();

void Start() {
    MyDelegate myDelegate;
    myDelegate = delegate() {
        Debug.Log("Test");
    };

    myDelegate.Invoke();
}
```
```C#
// 예시
delegate int MyDelegate(int a, int b);

void Start() {
    MyDelegate myDelegate;
    myDelegate = delegate(int a, int b) {
        return a + b;
    };

    int result = myDelegate.Invoke(1, 2);
}
```
## 람다식
익명 메서드를 더욱 쓰기 편하게 발전시킴

```C#
// 예시
delegate void MyDelegate();

void Start() {
    MyDelegate myDelegate;
    myDelegate = () => Debug.Log("Test");

    myDelegate.Invoke();
}
```
```C#
// 예시
delegate int MyDelegate(int a, int b);

void Start() {
    MyDelegate myDelegate;
    myDelegate = (int a, int b) => a + b;

    int result = myDelegate.Invoke(1, 2);
}
```

여러 줄 형태의 코드 블럭도 중괄호로 묶어 작성 가능

```C#
// 예시
delegate int MyDelegate(int a, int b);

void Start() {
    MyDelegate myDelegate;
    myDelegate = (int a, int b) => {
        Debug.Log("Test");
        return a + b;
    }

    int result = myDelegate.Invoke(1, 2);
}
```

### 단점

- -= 연산자로 체인을 끊을 수 없으므로 체인 관리가 필요한 경우 사용할 수 없음
- 남용 시 코드 분석 및 관리가 어려워질 수 있음
