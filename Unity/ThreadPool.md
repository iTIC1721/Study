# 📝Thread Pool

###### 참조
[Thread](./Thread.md)

---

**미리 생성된 스레드의 집합**  
필요할 때마다 스레드를 꺼내 쓰고, 필요 없어지면 다시 풀에 반환

스레드는 운영체제의 커널 자원을 사용하여 생성되기 때문에, **일회성으로 생성되고 종료되는 스레드**를 매번 생성하는 것은 성능상의 부하가 큼 → **스레드 풀을 이용해 재사용하는 것이 성능상 유리**

## 사용 방법

재사용할 스레드가 없으면 자동으로 스레드를 생성해주고, 있다면 해당 스레드를 재사용함  
사용한 스레드는 다시 풀에 저장해 재사용할 수 있도록 관리  
사용자는 스레드 생명주기에 신경쓰지 않아도 됨

`ThreadPool.SetMinThreads(스레드 개수, 비동기 I/O Thread 개수)`: 최소로 빌려올 수 있는 스레드 개수  
`ThreadPool.SetMaxThreads(스레드 개수, 비동기 I/O Thread 개수)`: 최대로 빌려올 수 있는 스레드 개수  

### 매개변수가 없는 스레드 풀
```C#
// 예시
void Start() {
    ThreadPool.QueueUserWorkItem(TestTP);
    // 별도의 스레드 생성 없음
    // Start() 없어도 바로 실행됨
}

void TestTP() {
    Debug.Log("Test");
}
```

### 매개변수가 있는 스레드 풀
```C#
// 예시
void Start() {
    int value = 1;
    ThreadPool.QueueUserWorkItem(TestTP, value);
    // 별도의 스레드 생성 없음
    // Start() 없어도 바로 실행됨
}

void TestTP(object value) {
    Debug.Log($"Test: {value}");
}
```

## 실행 순서 관련

일반적인 스레드와 동일하게, 스레드의 실행 순서는 운영체제의 스케줄링 방식에 따른 그때그때마다의 우선순위로 결정되기 때문에 **동일한 실행 순서를 보장할 수 없음**

```C#
// 예시
void Start() {
    for (int i = 0; i < 10; i++) {
        ThreadPool.QueueUserWorkItem(TestTP, i);
    }
    // 1 -> 10 순서로 실행되지 않음
    // 실행 시마다 순서가 바뀔 수 있음
}

void TestTP(object value) {
    Debug.Log($"Test: {value}");
}
```
