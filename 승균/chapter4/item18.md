# 아이템 18: 상속보다는 컴포지션을 사용하라
여기서 말하는 상속은 인터페이스로 확장하는 상속이 아닌 클래스가 다른 클래스를 확장하는 구현 상속을 말함.
## **핵심 정리**

**메소드 호출과 달리 상속은 캡슐화를 깨뜨린다.**
- 상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있음.
- 하위 클래스에 얼마나 영향을 끼칠지 여파를 알 수 없음. 

**잘못된 예 - 상속을 잘못 사용**
```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    // 추가된 원소의 수
    private int addCount = 0;

    public InstrumentedHashSet() {
    }

    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }
    
    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.adAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }
}

```

3개의 원소를 addAll메서드로 더했다고 가정 하였다. 
s.addAll(List.of("틱", "탁탁", "펑"));

3을 반환 기대 but 6 반환 -> why? HashSet의 addAll 메서드가 add 사용하여 구현한 문제가 있음. 


이러한 문제점을 상속 대신 컴포지션을 사용하여 해결 할 수 있음. 

**래퍼 클래스 - 상속 대신 컴포지션을 사용했다.**

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;
    
    publi
}
```