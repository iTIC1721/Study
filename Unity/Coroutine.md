# 📝Coroutine

IEnumerator를 이용하여 여러 프레임에 걸쳐 실행되는 메서드  
Update와 달리 메서드의 제어권을 유니티에 반환하고, 특정 조건이 되면 다시 진행

## 사용 방법

#### 실행
`public Coroutine StartCoroutine(IEnumerator routine);`

#### 종료
`public void StopCoroutine(IEnumerator routine);`  
`public void StopCoroutine(Coroutine routine);` ← 권장
###### 현재 객체가 실행 중인 전체 코루틴 종료
`public void StopAllCoroutines();`

## YieldInstruction 클래스
얼마나 대기할지 등 작동 방식을 전달하는 클래스  
이 클래스를 상속받은 Wait~~ 클래스를 통해 대기 기능을 구현

`yield return null`: 1프레임 대기  
`yield return new WaitForSeconds(float second)`: second초 동안 대기  
`yield return new WaitForSecondsRealtime(float second)`: second초 동안 대기 (timeScale 무시)  
`yield return new WaitForEndOfFrame()`: 현재 프레임 렌더링이 끝날 때까지 대기  
`yield return new WaitForFixedUpdate()`: 다음 FixedUpdate 프레임까지 대기  
`yield return new WaitUntil(Func<bool> predicate)`: 조건이 true가 될 때까지 대기  
`yield return new WaitWhile(Func<bool> predicate)`: 조건이 false가 될 때까지 대기  
###### 기타
`yield return StartCoroutine(...)`: 다른 코루틴이 끝날 때까지 대기


## 참고사항
- StartCoroutine을 사용할 때마다 가비지가 발생하므로, 꼭 필요한 곳에만 사용하는 것이 성능적으로 유리  
(반복적으로 실행해야 한다면 Update를 이용)
- YieldInstruction 역시 클래스이므로 매번 new로 생성하면 가비지가 발생, 캐싱해서 재활용하는 방식으로 가비지를 줄일 수 있음
```C#
// 예시
IEnumerator TestCoroutine() {
    WaitForSeconds wfs = new WaitForSeconds(1.0f);

    yield return wfs;
    Debug.Log("1");
    yield return wfs;
    Debug.Log("2");
    yield return wfs;
    Debug.Log("3");
}
```
