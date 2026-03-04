# 📝LINQ

**L**anguage **IN**tegrated **Q**uery  
데이터 가공 및 탐색을 위한 질의(Query) 문법

## 사용 방법

`using System.Linq;` 선언을 통해 사용 가능 

```C#
// 예시
class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public int Height { get; set; }

    public void Result()
    {
        Debug.Log($"Name : {Name}, Age : {Age}, Height : {Height}");
    }
}

void Start()
{
    List<Person> people = new List<Person>()
    {
        new Person { Name = "Tony", Age = 20, Height = 190 },
        new Person { Name = "Jessy", Age = 25, Height = 167 },
        new Person { Name = "Han", Age = 42, Height = 175}
    };

    var result1 = from person in people
                  select person;
    
    foreach (var one in result1) {
        one.Result();
    }
}
```

기본 사용법
```C#
var result1 = from person in people
              where person.Age < 40     // 조건이 필요 없다면 생략 가능
              select person;
```
▶ people에 속해 있는 person들 중 Age가 40살 미만인 person을 선택  
이때 result1은 IEnumerable\<Person> 타입

### 참고사항
LINQ는 내부적으로 아래와 같이 확장 메서드 형태로 변형됨 (셋 모두 같은 동작)
```C#
var result1 = from person in people
              select person;

-->

// IEnumerable<T>의 확장 메서드
var result1 = people.Select((person) => person);

-->

// 확장 메서드의 구현부
IEnumerable<Person> SelectAll(List<Person> people) {
    foreach (Person item in people) {
        yield return item;
    }
}
```

## 기본 구문

### 1. from ~ in

모든 쿼리식은 from 절로 시작  
쿼리식의 대상이 될 **데이터 원본**과 데이터 원본 안에 들어있는 각 요소를 나타내는 **범위 변수**를 지정해야 함

데이터 원본은 반드시 **IEnumerable\<T>를 상속**받은 객체여야 함

```C#
from 범위변수 in 데이터 원본
```

### 2. where

from 절에서 얻은 범위 변수가 가져야 할 조건을 정함

```C#
from 범위변수 in 데이터 원본
where 조건

// 예시
from person in people
where person.Age < 40
```

### 3. orderby

데이터의 정렬을 수행  
정렬 대상(범위 변수)는 **IComparable을 상속**받아야 함

```C#
// 예시: int 배열에서 숫자들을 정렬하여 뽑아냄
from one in numbers
orderby one descending      // descending 내림차순 ascending 오름차순(기본값)
```

### 4. select

최종 결과를 추출  
익명 타입으로 결과를 재가공할 수 있음

```C#
// 예시: int 배열에서 숫자들의 제곱을 뽑아내어 추출
from one in numbers
orderby one descending
select new { Result = one * one };      // 익명 타입으로 재가공
```

### 5. group ~ by ~ into

데이터의 그룹화

```C#
group 범위변수 by 분류기준 into 그룹 변수

// 예시
from one in numbers
group one by one > 2 into g
select g;
```

이때 그룹 변수 g는 IGrouping 인터페이스를 상속받은 타입
`public interface IGrouping<TKey, TElement> : IEnumerable<TElement>, IEnumerable`
- TKey: 그룹의 키 - 위 예시에서는 one > 2를 기준으로 그룹화했으므로 TKey는 bool 타입
- TElement: 그룹의 요소 타입 - 위 예시에서는 int 타입

```C#
// 예시
from str in stringArr
group str by str.Length into wordGroup
orderby wordGroup.Key                       // 별다른 표시가 없으므로 ascending
select wordGroup;

==> 문자열의 길이를 key로 가지는 그룹 (키별로 오름차순 정렬)
```

### 6. join

두 데이터 원본을 연결

#### 내부 조인
기준 데이터 원본에는 존재하지만 연결할 데이터 원본에 존재하지 않는 데이터는 **조인 결과에 포함하지 않음**
```C#
from a in A                             // 기준 데이터 원본
join b in B on a.XXX equals b.YYY           // 연결할 데이터 원본 및 조인 조건
```

#### 외부 조인
기준 데이터 원본에는 존재하지만 연결할 데이터 원본에 존재하지 않는 데이터를 **조인 결과에 포함함**
```C#
from a in A                             // 기준 데이터 원본
join b in B on a.XXX equals b.YYY into ZZZ           // 연결할 데이터 원본 및 조인 조건
from c in ZZZ.DefaultIfEmpty()
```

```C#
// 예시
int damage = 10;

var monster = from one in monsters
              join data in monsterDatas on one.Name equals data.name into result
              from joinData in result.DefaultIfEmpty(new MonsterData { Name = "없음" })
              select new {
                  Name = joinData.Name,
                  Hp = joinData.Hp - damage
              };

foreach (var one in monster) {
    Debug.Log($"Name: {one.Name}, 남은 HP: {one.Hp}");
}
```
▶ monsters에 있는 모든 행은 전부 출력하되, monsterDatas에 있는 이름을 가진 행은 정상적으로 출력하고, monsterDatas에 이름이 없는 행은 Name = "없음"인 기본 데이터로 출력
