# 📝옵저버 패턴

###### 참조
[델리게이트](../Unity/Delegate.md)  
[Action / Func](../Unity/ActionFunc.md)  

---

어떤 객체의 상태가 변경되었을 때, 그 객체를 관찰하고 있는 다른 객체들(옵저버)에게 자동으로 변경 사항을 통지하는 구조

ex) 데미지를 입었을 때 → UI도 변경하고, 피격 사운드도 재생하고, 피격 애니메이션도 출력하는 등 여러 일을 해야 할 때 모든 일을 각각의 클래스에서 담당하면 유지보수가 어려워짐  
▶ 데미지를 받는 이벤트가 발생(Subject) 시 각각의 클래스(Observer)로 알림을 보내는 방식으로 구현

## 기본 구조
1. Subject: **발신자**, 상태가 바뀌는 객체, 이벤트를 발생시킴
2. Observer: **수신자**, 이벤트를 구독하고 알림을 듣는 객체들
3. Notification: **알림**, Subject가 Observer에게 알리는 방식 (보통 C#의 event 사용)

### 예제
```C#
// Subject: 체력 관리 클래스
public class PlayerHealth : MonoBehaviour {
    public event Action<int, int> OnHealthChanged; // 체력 변화를 감지할 event (Action으로 구현)

    private int currentHealth;
    private int maxHealth = 100;

    private void Start()
    {
        currentHealth = maxHealth;
    }

    public void TakeDamage(int amount)
    {
        currentHealth = Mathf.Max(currentHealth - amount, 0);
        OnHealthChanged?.Invoke(currentHealth, maxHealth);        // 데미지를 입었을 때 체력 변화 event 알림 발신
    }

    public void Heal(int amount)
    {
        currentHealth = Mathf.Min(currentHealth + amount, maxHealth);
        OnHealthChanged?.Invoke(currentHealth, maxHealth);        // 힐을 받았을 때 체력 변화 event 알림 발신
    }
}
```

```C#
// Observer: 체력 UI 업데이트
public class HealthBar : MonoBehaviour
{
    [SerializeField] private PlayerHealth playerHealth;
    [SerializeField] private Image fillImage;

    private void OnEnable()
    {
        playerHealth.OnHealthChanged += UpdateHealthBar;
    }

    private void OnDisable()
    {
        playerHealth.OnHealthChanged -= UpdateHealthBar;
    }

    private void UpdateHealthBar(int current, int max)
    {
        fillImage.fillAmount = (float)current / max;
    }
}
```

```C#
// Observer: 피격 사운드 재생
public class HitSoundPlayer : MonoBehaviour
{
    [SerializeField] private PlayerHealth playerHealth;
    [SerializeField] private AudioSource hitSound;

    private void OnEnable()
    {
        playerHealth.OnHealthChanged += PlayHitSound;
    }

    private void OnDisable()
    {
        playerHealth.OnHealthChanged -= PlayHitSound;
    }

    private void PlayHitSound(int current, int max)
    {
        if (current < max) hitSound.Play();
    }
}
```

하나의 이벤트 OnHealthChanged를 여러 오브젝트에서 구독함으로써, 플레이어는 오직 자신의 상태만 관리하고, 다른 시스템은 이벤트를 통해 알아서 반응할 수 있게 됨

## 주의점
- 이벤트 구독(+=)을 했다면, 반드시 OnDisable() 또는 OnDestroy()에서 -=로 **구독 해제**를 해야 함 (메모리 누수 또는 Exception 발생 가능)
- 옵저버 수가 많아질수록 구조가 복잡해질 수 있으므로, 로그를 남기거나 관리 시스템을 도입하는 것도 좋음
