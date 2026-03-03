# 📝확장 메서드

기존 클래스의 기능을 확장하는 기법  
**클래스의 외부에서 클래스의 메서드처럼 사용할 수 있는, 새로운 메서드를 만들 수 있는 기능**

### 사용하는 경우
1. sealed로 선언된 클래스는 상속할 수 없음 (ex. String 등 C#에 이미 정의된 클래스 대부분)  
2. 상속으로 기능을 확장하면 기존 클래스를 사용하던 코드의 수정이 필요함 (상속받은 새로운 클래스로 교체해야함): 호환성 문제 등 관리의 어려움

## 사용 방법
- static class + static method
- 첫 번째 매개변수에 this 키워드

```C#
// 예시: int 클래스를 확장
static class IntExtension {
    static int Square(this int num) {
        return num * num;
    }

    static string AttachString(this int num, string attach) {
        return num + " " + attach;
    }
}

void Start() {
    int a = 10;

    Debug.Log(a.Square());                // 100
    Debug.Log(a.AttachString("Test"));    // 10 Test
}
```

### 참고사항

확장 메서드는 기본 메서드에 비해 우선순위가 낮음  
→ 확장 메서드로 같은 이름과 같은 매개변수를 사용한다면 기존에 있던 메서드에 가려짐
