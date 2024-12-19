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
    
    public InstrumentedSet(Set<E> s) {
        super(s);
    }
    
    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends  E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }
}
```

**재사용할 수 있는 전달 클래스**
```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }
    
    public void clear() { s.clear(); }
    public boolean contains(Object o) { return s.contains(o); }
    public boolean isEmpty() { return s.isEmpty(); }
    public int size() { return s.size(); }
    public Iterator<E> iterator() { return s.iterator(); }
    public boolean add(E e) { return s.add(e); }
    public boolean remove(Object o) { return s.remove(o); }
    public boolean containsAll(Collection<?> c) { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c) { return s.adAll(c); }
    public boolean removeAll(Collection<?> c) { return s.adAll(c); }
    public boolean retainAll(Collection<?> c) { return s.retainAll(c); }
    public Object[] toArray() { return s.toArray(); }
    public <T> T[] toArray() { return s.toArray(a); }
    @Override public boolean equals(Object o) { return s.equals(o); }
    @Override public int hashCode() { return s.hashCode(); }
    @Override public String toString() { return s.toString(); }
}
```

정의되어 있는 Set 기능을 덧씌워 새로운 Set으로 만드는 것이 이 클래스의 핵심임. <br/>
상속 방식은 구체 클래스 각각을 따로 확장해야 하며, 지원하고 싶은 상위 클래스의 생성자 각각에 대응하는 생성자를 별로도 정의 <br/>
하지만 위에 코드는 한번만 구현해두면 어떠한 Set구현체라도 사용 할 수 있음 <br/>

InstrumentedSet 사용하면 대상 Set 인스턴스를 특정 조건하에서만 임시로 계측
```java
static void walk(Set<Dog> dogs) {
    InstrumentdSet<Dog> iDogs = new InstrumentedSet<>(dogs);
    ... // 이 메서드에서는 dog 대신 iDogs를 사용한다.
}
```

Set 인스턴스를 감싸고 (wrap) 있다는 뜻에서 InstrumentedSet 같은 클래스를 래퍼 클래스라 하며, 다른 Set에 계측 기능을 씌운다는 뜻으로 데코레이터 패턴이라고 한다.  <br/>

래퍼 클래스는 단점이 거의 없음. 한 가지, 래퍼 클래스가 콜백(callback) 프레임워크와는 어울리지 않는다는 점만 주의 <br/>
콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출때 사용하도록 한다. <br/>
내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출 << SELF 문제<br/>

상속은 반드시 하위 클래스가 상위 클래스인 경우 쓰여야 한다. <br/>
클래스 B가 클래스 A와 is-a 관계일 때만 클래스 A를 상속해야 한다.


**[궁금한 점]**
1. 왜 콜백 할 때, 자기 자신이 호출되는지
