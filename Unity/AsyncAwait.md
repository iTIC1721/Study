# 📝async / await

###### 참조
[Task](./Task.md)

---

## BeginInvoke(), EndInvoke()
비동기 실행을 통해 프로그램이 병렬적으로 실행되게 하여 작업(주로 I/O 작업 등)이 효율적으로 실행될 수 있게 함

반환값이나 콜백이 필요하지 않다면 Thread, ThreadPool, Task로도 구현할 수 있지만, 반환값이나 콜백이 필요한 경우 BeginInvoke(), EndInvoke()를 사용할 수 있음

```C#
IAsyncResult BeginInvoke(
    Param params,              // 델리게이트의 매개변수 (임의 타입, 임의 개수 가능)
    AsyncCallback callback,    // 작업 완료 후 실행할 콜백 
    object state               // 콜백에 넘길 사용자 정의 데이터, 생략 가능
);
```
```C#
ReturnType EndInvoke(IAsyncResult asyncResult);    // 반환값은 실행한 델리게이트의 반환값
```
BeginInvoke는 어떤 델리게이트를 백그라운드 스레드로 돌리며, 스레드 풀에 넣어서 실행함  
델리게이트 실행 완료 시 매개변수로 넣은 콜백 메서드를 실행

EndInvoke는 매개변수로 받은 IAsyncResult를 이용해 실행한 델리게이트의 리턴값을 반환함

```C#
// 예시
string filePath;
string fileContents;

void Start()
{
    // Application.dataPath = Assets 폴더 경로
    filePath = Path.Combine(Application.dataPath, "Resources/File.txt");
    // 델리게이트 생성
    Func<string> readFileDelegate = new Func<string>(ReadFile);
    // 비동기 호출 시작
    IAsyncResult result = readFileDelegate.BeginInvoke(ReadFileCallback, readFileDelegate);

    Debug.Log("메인 스레드 진행 중");
}

string ReadFile()
{
    if (File.Exists(filePath))
    {
        return File.ReadAllText(filePath);
    }
    return null;
}

void ReadFileCallback(IAsyncResult ar)
{
    // 델리게이트로부터 결과를 가져옴  ar.AsyncState 에 readFileDelegate 저장됨
    Func<string> readFileDelegate = (Func<string>)ar.AsyncState;
    fileContents = readFileDelegate.EndInvoke(ar);

    // 비동기 작업 완료 후 결과 출력
    Debug.Log("파일 읽기 완료: " + fileContents);
}
```

## async, await

BeginInvoke(), EndInvoke()보다 더 간결하게 구현 가능

### 사용 방법
`using System.Threading.Tasks;` 선언을 통해 사용 가능

```C#
async void Start() {
    Debug.Log("Test 시작");
    int result = await SumAsync(10, 20);    // 비동기로 실행할 부분에 await 키워드 사용
    Debug.Log("결과: " + result);
}

async Task<int> SumAsync(int a, int b) {
    await Task.Delay(3000);        // 3초 지연
    return a + b;
}
```
- 비동기 작업을 사용할 메서드에 async 키워드 사용  
- 비동기로 실행할 코드 부분에 await 키워드 사용

await로 지정된 부분에서 해당 async 메서드를 잠시 중지하고, await 부분이 완료되면 다시 실행  
이때, **스레드 전체를 멈추는 것이 아님!**

예외 처리 및 메서드의 완료 추적을 위해, 일반적으로 async로 선언하는 메서드의 반환타입은 Task 또는 Task\<TResult>

### 장점
1. 비동기 코드를 좀 더 이해하기 쉽게 구현
2. 동기 코드와 비슷하게 작성할 수 있어서 가독성이 좋음
3. 스레드를 차단하지 않기 때문에 시스템이 멈추는 문제가 없음 - 안정성이 좋음
