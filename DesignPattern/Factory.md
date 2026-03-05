# 📝팩토리 패턴

객체 생성 과정을 캡슐화  
다양한 종류의 객체를 생성할 때 확장성과 유지보수를 크게 높여줄 수 있음

## 팩토리 메서드 패턴

객체 생성 방법을 서브 클래스에서 정의하도록 함

```C#
public interface IFactory {
    public void Create();
}
```
```C#
public class SlimeFactory : IFactory {
    public GameObject slime;
    
    public void Create() {
        Instantiate(slime);
    }
}

public class ZombieFactory : IFactory {
    public GameObject zombie;
    
    public void Create() {
        Instantiate(zombie);
    }
}
```
```C#
public class Test : MonoBehaviour {
    public IFactory factory;
    
    void Start() {
        factory.Create();
    }
}
```

## 전통적인 팩토리 메서드 패턴

생산되는 클래스까지 추상화

```C#
public abstract class Monster : MonoBehaviour {
    public abstract void Attack();
}
```
```C#
public class Slime : Monster {
    public override void Attack() {
        Debug.Log("Slime Attack!");
    }
}

public class Zombie : Monster {
    public override void Attack() {
        Debug.Log("Zombie Attack!");
    }
}
```
몬스터 추상 클래스 제작
```C#
public interface IFactory {
    public Monster Create();
}
```
```C#
public class SlimeFactory : IFactory {
    public Monster slime;
    
    // 반환타입이 몬스터 추상 클래스
    public Monster Create() {
        return Instantiate(slime);
    }
}

public class ZombieFactory : IFactory {
    public Monster zombie;
    
    public Monster Create() {
        return Instantiate(zombie);
    }
}
```
```C#
public class Test : MonoBehaviour {
    public IFactory factory;
    
    void Start() {
        Monster monster = factory.Create();
        monster.Attack();                       // factory에 넣은 팩토리에 따라 실행 결정
    }
}
```

## 추상 팩토리 패턴

**특정 제품군**을 생산할 때 사용되는 방법
한 팩토리에서 한 제품이 아닌 여러 제품군을 생산

```C#
public abstract class Monster : MonoBehaviour {
    public abstract void Attack();
}
```
```C#
// 불속성
public class FireSlime : Monster {
    public override void Attack() {
        Debug.Log("FireSlime Attack!");
    }
}

public class FireZombie : Monster {
    public override void Attack() {
        Debug.Log("FireSlime Attack!");
    }
}
```
```C#
// 얼음속성
public class IceSlime : Monster {
    public override void Attack() {
        Debug.Log("IceSlime Attack!");
    }
}

public class IceZombie : Monster {
    public override void Attack() {
        Debug.Log("IceZombie Attack!");
    }
}
```
제품**군**별로 생성
```C#
public abstract class Factory {
    public Monster CreateSlime();
    public Monster CreateZombie();
}
```
```C#
public class FireFactory : IFactory {
    public Monster fireSlime;
    public Monster fireZombie;
    
    public Monster CreateSlime() {
        return Instantiate(fireSlime);
    }
    
    public Monster CreateZombie() {
        return Instantiate(fireZombie);
    }
}

public class IceFactory : IFactory {
    public Monster iceSlime;
    public Monster iceZombie;
    
    public Monster CreateSlime() {
        return Instantiate(iceSlime);
    }
    
    public Monster CreateZombie() {
        return Instantiate(iceZombie);
    }
}
```
```C#
public class Test : MonoBehaviour {
    public Factory factory;
    
    void Start() {
        Monster slime = factory.CreateSlime();
        monster.Attack();                       // factory에 넣은 팩토리에 따라 실행 결정
        
        Monster zombie = factory.CreateZombie();
        monster.Attack();                       // factory에 넣은 팩토리에 따라 실행 결정
    }
}
```




