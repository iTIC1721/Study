# 📝Task

###### 참조
[Thread](./Thread.md)

---

스레드와 스레드 풀의 단점을 개선한 클래스  
실행할 때 내부적으로 스레드 풀을 이용하여 스레드를 생성하며, Task 객체 자체는 **어떤 작업의 상태를 나타내고 스레드로부터 콜백을 받는 객체**임

## 사용 방법

`using System.Threading.Tasks;` 선언을 통해 사용 가능

스레드처럼 Action 델리게이트를 매개변수로 받는 생성자를 통해 생성 가능하며, Start() 메서드로 실행  
람다식 역시 매개변수로 전달할 수 있음
```C#
Task task1 = new Task(TestAction);
Task task2 = new Task(() => Debug.Log("test"));

task1.Start();
task2.Start();
```

### Wait

스레드에서의 Join()과 비슷하게, 지정한 Task가 완료될 때까지 현재 스레드를 대기시킴
```C#
Task task = new Task(TestAction);

task.Start();
task.Wait();
```

### ContinueWith

Task가 완료된 후 실행할 작업을 지정할 수 있음
`Task ContinueWith(Action<Task> continuationAction);`  
```C#
Task task = new Task(TestAction);
task.ContinueWith((obj) => Debug.Log("test"));

task.Start();
```
이때 obj는 이전 Task로, task 변수의 값이 새로운 Task로 교체되었을 수 있기 때문에 이전 Task의 상태 확인 등을 위해 매개변수로 받음

### Run

Task의 생성과 시작을 동시에 하는 메서드  
new 키워드와 Start() 메서드가 없어도 됨
```C#
Task task = Task.Run(TestAction);
```

#### Task.Factory.StartNew()

Run() 메서드보다 더 많은 옵션을 선택할 수 있는 생성+시작 메서드

- CancellationToken: 작업 취소 기능 제공
- TaskCreationOption: Task 생성 시 추가적인 옵션 설정
- TaskScheduler: Task가 실행될 스케줄러 지정

### WhenAll

매개변수로 있는 모든 Task가 완료되었을 때 함께 완료되는 Task 객체를 반환함  
Result는 없으며 IsCompleted로 완료 여부를 체크할 수 있음

```C#
Task task1 = Task.Run(TestAction);
Task task2 = Task.Run(() => Debug.Log("test"));

Task task3 = Task.WhenAll(task1, task2);
```
task3은 task1과 task2가 모두 완료되었을 때 완료 상태로 전환됨

### WhenAny

매개변수로 있는 모든 Task 중 하나가 완료되었을 때 함께 완료되는 Task 객체를 반환함  
Result는 처음 완료된 Task이며 IsCompleted로 완료 여부를 체크할 수 있음

```C#
Task task1 = Task.Run(TestAction);
Task task2 = Task.Run(() => Debug.Log("test"));

Task task3 = Task.WhenAny(task1, task2);
```
만약 task2가 먼저 완료되었다면 task3은 그때 완료 상태로 전환되며, task3.Result == task2가 됨

### Task\<TResult\>

반환값이 있는 Task로, Action 대신 Func를 매개변수로 받아 생성됨
반환값은 task.Result에 저장됨

```C#
Task<int> task1 = Task.Run(() => 1);
Task<float> task2 = Task.Run(() => 0.2f);
```
