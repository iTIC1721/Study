# 📝LINQ 메서드

LINQ 쿼리 기본 구문은 유용하지만, 일부 특정한 상황에서는 LINQ 메서드의 사용이 더 적절할 수 있음

### 1. Where 메서드
조건에 맞는 요소들만 필터링
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var evenNumbers = numbers.Where(n => n % 2 == 0);   // 짝수 요소만 반환
```

### 2. OrderBy 메서드
요소들을 정렬
- OrderBy: 오름차순
- OrderByDescending: 내림차순
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var orderNumbers1 = numbers.OrderBy(n => n);      // 오름차순 정렬
var orderNumbers2 = numbers.OrderByDescending(n => n);      // 내림차순 정렬
```

### 3. Select 메서드
요소들을 재가공해서 추출
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var squareNumbers = numbers.Select(n => n * n);   // 각 요소를 제곱하여 추출
```

### 4. GroupBy 메서드
요소들을 특정 키를 기준으로 그룹화
```C#
string[] strs = { "a", "bb", "cc", "ddd", "eeeee" };

var groups = strs.GroupBy(s => s.Length);       // 글자수 기준으로 그룹화
```

### 5. Join 메서드
두 컬렉션을 기준으로 조인 수행  
보다 직관적인 쿼리 구문 사용을 추천
```C#
기준컬렉션.Join(합칠컬렉션, 기준컬렉션조건, 합칠컬렉션조건, 결과); // 기준컬렉션조건 == 합칠컬렉션조건일 때 조인

// 예시
var joinTest = monsters.Join(monsterDatas, monster => monster.Name, data => data.Name, 
                             (monster, data) => new { monster.Name, data.Hp });
```

### 6. Sum, Average, Min, Max
컬렉션의 합, 평균, 최소값, 최대값을 구함
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var sum = numbers.Sum();        // 15
var avg = numbers.Average();    // 3
var min = numbers.Min();        // 1
var max = numbers.Max();        // 5
```

### 7. First, FirstOrDefault, Last, LastOrDefault
컬렉션에서 조건에 맞는 첫번째/마지막 요소를 구함  
FirstOrDefault/LastOrDefault의 경우 조건에 맞는 요소가 없을 경우 각 타입의 기본값을 반환
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var fst = numbers.First();                              // 1
var fstDft = numbers.FirstOrDefault(n => n % 2 == 0);   // 2
var lst = numbers.Last();                               // 5
var lstDft = numbers.LastOrDefault(n => n % 2 == 0);    // 4
```

### 8. Any, All
Any는 컬렉션에서 조건에 맞는 요소가 하나 이상 있는지, All은 컬렉션의 모든 요소가 조건에 맞는지 확인
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var any = numbers.Any(n => n % 2 == 0);     // true
var all = numbers.All(n => n % 2 == 0);     // false
```

### 9. Contains
컬렉션이 해당 요소를 포함하는지 확인
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var contain = numbers.Contains(10);     // false
```

### 10. Distinct
컬렉션에서 중복된 요소를 제거
```C#
int[] numbers = { 1, 1, 2, 2, 2 };

var distinct = numbers.Distinct();      // { 1, 2 }
```

### 11. Take, Skip
Take는 지정된 수만큼 요소를 가지고 오고, Skip은 지정된 수만큼 요소를 건너뜀
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

var take = numbers.Take(3);         // { 1, 2, 3 }
var skip = numbers.Skip(3);         // { 4, 5 }
```

### 12. Aggregate
컬렉션 요소들을 누적하여 단일 결과로 만듦
```C#
int[] numbers = { 1, 2, 3, 4, 5 };

// 1) acc = 1, n = 2 => 1 * 2 = 2
// 2) acc = 2, n = 3 => 2 * 3 = 6
// 3) acc = 6, n = 4 => 6 * 4 = 24
// 4) acc = 24, n = 5 => 24 * 5 = 120
var mul = numbers.Aggregate((acc, n) => acc * n);   // 120

// 같은 방식으로
var sum = numbers.Aggregate((acc, n) => acc + n);   // 15
```

### 13. SelectMany
컬렉션의 하위 요소들을 펼쳐서 하나의 요소로 만듦
```C#
List<List<string>> lists = new List<List<string>>
{
    new List<string> {"a", "b"},
    new List<string> {"c", "b"},
    new List<string> {"a", "d"}
}

var selectList = lists.SelectMany(li => li);    // { "a", "b", "c", "d" }

List<Dictionary<string, int>> lists = new List<Dictionary<string, int>>
{
    new Dictionary<string, int> { {"a", 1}, {"b", 2} },
    new Dictionary<string, int> { {"c", 3}, {"d", 4} },
}

var selectList = lists.SelectMany(li => li);    // 하나의 딕셔너리로 통합
```
