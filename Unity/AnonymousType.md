# 📝익명 타입

타입 및 이름의 직접 구현 없이도 타입을 사용할 수 있게 해주는 문법  
C# 3.0부터 지원

주로 LINQ의 결과를 담거나 임시 데이터 구조를 만드는 데에 사용

## 사용 방법
```C#
Person person1 = new Person { Name = "Kim", Age = 25, Height = 180 };  // 일반적인 타입

var person2 = new { Name = "Lee", Age = 20, Height = 177 };            // 익명 타입
```

var로 변수를 선언하면, new **Person**처럼 타입을 적지 않아도 new 키워드만 사용한다면 내부적으로 AnonymousType을 지정해줌

**같은 구조**의 객체를 여러개 만들면 모두 같은 타입으로 간주 (순서까지 동일해야 함)

### 익명 타입 배열
```C#
var list = new[]
{
    new { Name = "Kim", Age = 20 },
    new { Name = "Lee", Age = 22 },
    new { Name = "Park", Age = 25 }
};
```
같은 구조의 타입은 배열로 만들 수 있음  
이 경우 선언 시 `new[]`로 선언

### 주의사항
메서드의 반환 타입으로 익명 타입을 지정하고 싶은 경우, 타입의 이름을 적을 수 없으므로 `object`나 `dynamic`으로 지정해야 함

