# 📝Thread

운영체제가 CPU에 일을 시키는 기본 단위  
(명령어를 실행하기 위한 스케줄링 단위)

### 멀티 스레드

#### 장점
- 동시에 여러 작업을 할 수 있음
- 하나의 프로세스 내에서 같은 데이터/변수를 사용할 수 있어 데이터 공유가 쉬움
- 이미 실행된 프로세스의 자원을 사용하기 때문에 메모리를 절약할 수 있음 

#### 단점
- 구현이 복잡함
- 하나의 스레드에서 문제가 생기면 전체 프로세스가 멈출 수 있어 안정성이 낮음
- 컨텍스트 스위칭 과정에서도 많은 코스트를 소모하기 때문에 성능 저하를 유발

## 사용 방법

`using System.Threading;` 선언을 통해 사용 가능

유니티에서는 따로 스레드를 만들지 않아도 "게임 로직 스레드"라는 스레드가 기본적으로 존재

#### 매개변수가 없는 스레드 실행
```C#
// 예시
Thread thread;

void Start() {
    thread = new Thread(new ThreadStart(Test));    // ThreadStart 생성자의 인수에 델리게이트 방식으로 실행 대상 메서드를 넘김
    thread.Start();    // 스레드 실행
}

void Test() {
    Debug.Log("스레드 시작");
    Debug.Log(thread.ThreadState);

    Thread.Sleep(2000);    // 2000ms동안 스레드 중지

    Debug.Log("스레드 종료");
}
```

★ ThreadStart를 사용하지 않고 바로 메서드 이름을 인수로 넘겨도 컴파일러에서 자동으로 ThreadStart를 통해 넘긴 것처럼 변환함  
`thread = new Thread(Test);`

#### 매개변수가 있는 스레드 실행
```C#
// 예시
Thread thread;

void Start() {
    thread = new Thread(new ParameterizedThreadStart(Test));    // ParameterizedThreadStart 생성자의 인수에 델리게이트 방식으로 실행 대상 메서드를 넘김
    thread.Start(10);    // 스레드 실행
}

void Test(object num) {
    Debug.Log("스레드 시작");
    Debug.Log(thread.ThreadState);

    Thread.Sleep(2000);    // 2000ms동안 스레드 중지

    Debug.Log(num);
    Debug.Log("스레드 종료");
}
```

#### 매개변수가 여러개인 스레드 실행
object를 인수로 받으므로 여러 매개변수를 하나의 클래스 혹은 구조체로 묶어 사용
```C#
// 예시
public class Param {
    public int num1;
    public int num2;
}

Thread thread;

void Start() {
    thread = new Thread(new ParameterizedThreadStart(Test));    // ParameterizedThreadStart 생성자의 인수에 델리게이트 방식으로 실행 대상 메서드를 넘김
    
    Param param = new Param();
    param.num1 = 1;
    param.num2 = 2;
    thread.Start((object)param);    // 스레드 실행
}

void Test(object num) {
    Debug.Log("스레드 시작");
    Debug.Log(thread.ThreadState);

    Thread.Sleep(2000);    // 2000ms동안 스레드 중지

    Param param = (Param)num;
    Debug.Log(param.num1 + param.num2);
    Debug.Log("스레드 종료");
}
```

### Join

`Join()`: 현재 스레드를 잠시 멈추고, 다른 스레드가 끝날 때까지 대기

```C#
// 예시
Thread thread1, thread2;

void Start() {
    thread1 = new Thread(Test1);
    thread1.Start();
}

void Test1() {
    Debug.Log("스레드1 시작");
    thread2 = new Thread(Test2);
    thread2.Start();
    thread2.Join();                // Join
    Debug.Log("스레드1 종료");
}

void Test2() {
    Debug.Log("스레드2 시작");
    Thread.Sleep(2000);
    Debug.Log("스레드2 종료");
}
```
```
// 실행 결과
스레드1 시작
스레드2 시작
스레드2 종료
스레드1 종료
```

### Abort

`Abort()`: 스레드를 강제로 종료

종료 시 `ThreadAbortException`을 발생시키고 종료  
try-catch 구문을 통해 비정상적인 종료를 방지할 수 있음

실행 중인 스레드가 강제로 종료되어도 프로세스나 시스템 전체에 영향이 없는 경우에만 사용

### Interrupt

`Interrupt()`: 스레드가 WaitSleepJoin 상태일 때까지 기다려서 강제 종료

Wait(), Sleep(), Join() 등으로 WaitSleepJoin 상태가 될 때까지 대기

종료 시 `ThreadInterruptException`을 발생시키고 종료  
try-catch 구문을 통해 비정상적인 종료를 방지할 수 있음

Abort보다 좀 더 유연한 종료가 가능

### Background 스레드

프로그램 종료 시 함께 종료되는 스레드

- Foreground 스레드: 하나라도 살아있다면 프로세스가 종료되지 않음
- Background 스레드: 프로세스 종료를 막지 않음, 프로세스가 종료될 때 같이 종료

`thread.IsBackground = true;`를 통해 적용할 수 있음

로그 기록/캐시 정리 등 중요하지 않은 작업에 주로 사용
