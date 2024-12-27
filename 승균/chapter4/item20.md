# 아이템 20: 추상 클래스보다는 인터페이스를 우선하라

**핵심내용**
자바가 제공하는 다중 구현 메커니즘 인터페이스, 추상 클래스 두가지

자바 8부터 디폴트 메서드 제공 <- 두 메커니즘 모두 인스턴스 메서드를 구현 형태로 제공 할 수 있음.

**기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.** <br/>
Comparable, Iterable, AutoCloseable 인터페이스가 새로 추가 됐을 때, 표준 라이브러리의 수많은 기존 클래스가 인터페이스들을 구현한 채 릴리스

기존 클래스에 추상 클래스를 끼워넣는게 어려움 why? 모든 자손이 추상 클래스를 상속하기 때문임. => 문제되는 이유 필요 없는 기능이라도 상속되기 때문임.

**인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.**

믹스인이란? 무엇인가?

다른 클래스가 구현해야 하는 메서드 정의하지만, 독립적인 객체 상속 구조와 관계없이 추가적인 기능을 제공하는 데 사용되는 설계 패턴 <br/>
목적: 특정 기능을 다양한 클래스에 추가하기 위해 사용됨. <br/>
특징:
- 구현코드는 없고, 메서드 선언만 포함
- 주로 특정 기능(행위)을 강제하거나 제공하려는 의도로 사용됩니다.
- 상속 트리의 강제성을 줄이고 클래스에 필요한 동작만 추가

**동작별 기능 추가**
```java
// 믹스인 인터페이스 정의
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

// 기본 클래스
class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }

    void eat() {
        System.out.println(name + " is eating.");
    }
}

// 특정 기능을 믹스인으로 추가
class Bird extends Animal implements Flyable {
    Bird(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(name + " is flying!");
    }
}

class Fish extends Animal implements Swimmable {
    Fish(String name) {
        super(name);
    }

    @Override
    public void swim() {
        System.out.println(name + " is swimming!");
    }
}

class Duck extends Animal implements Flyable, Swimmable {
    Duck(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(name + " is flying like a duck!");
    }

    @Override
    public void swim() {
        System.out.println(name + " is swimming like a duck!");
    }
}

// 사용 예시
public class Main {
    public static void main(String[] args) {
        Bird bird = new Bird("Sparrow");
        Fish fish = new Fish("Goldfish");
        Duck duck = new Duck("Mallard");

        bird.eat();
        bird.fly();

        fish.eat();
        fish.swim();

        duck.eat();
        duck.fly();
        duck.swim();
    }
}
```

**로깅 기능 믹스인**
```java
interface Loggable {
    default void log(String message) {
        System.out.println("[LOG] " + message);
    }
}

class ServiceA implements Loggable {
    void process() {
        log("ServiceA processing...");
        System.out.println("ServiceA completed.");
    }
}

class ServiceB implements Loggable {
    void execute() {
        log("ServiceB executing...");
        System.out.println("ServiceB completed.");
    }
}

// 사용 예시
public class Main {
    public static void main(String[] args) {
        ServiceA serviceA = new ServiceA();
        ServiceB serviceB = new ServiceB();

        serviceA.process();
        serviceB.execute();
    }
}
```

**인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다.**

현실에는 계층을 엄격히 구분하기 어려운 개념도 존재함.

ex) 예시
```java
public interface Singer {
    AudioClip sing(Song s);
}

public interface Songwriter {
    Song compose(int chartPosition);
}
```

인터페이스로 둘 다 상속하면, Singer와 SongWriter 모두를 구현해도 문제되지 않는다. <br/>
심지어 Singer와 Songwriter 모두를 확장하고 새로운 메서드까지 추가한 제3의 인터페이스를 정의 <br/>

```java
public interface SingerSongwriter extends Singer, SongWriter {
    AudioClip strum();
    void actSensitive();
}
```

위에 같은 구조로 클래스로 만들려면 가능한 조합 전부를 각각 클래스로 정의해야함

타입을 추상 클래스로 정의해두면 크 타입에 기능을 추가하는 방법은 상속뿐이다.

상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기는 더 쉽다.

equals, hashCode 같은 Object 메서드 정의하고 있지만, 이들은 디폴트 메서드로 제공해서는 안됨.

인터페이스는 인스턴스 필드를 가질 수 없고 public이 아닌 정적 멤버도 가질 수 없음. <br/>
내가 만들지 않은 인터페이스 디폴트 메서드 추가 x <br/>

인터페이스와 추상 골격 구현(skeletal implementation) 클래스를 함께 제공하는 방식으로 인터페이스와 추상 클래스 장점 모두 취할 수 있다. <br/>
인터페이스로는 타입을 정의하고, 필요하면 디폴트 메서드 몇개도 함께 제공 <br/>

골격 구현 클래스는 나머지 메서드들까지 구현한다. 이렇게 해두면 단순히 골격 구현을 확장하는 것만으로 인터페이스를 구현하는데 필요한 일이 대부분 완료 <br/>
위에처럼 하는 방식이 템플릿 메서드 패턴이다. <br/>
관례상 인터페이스 이름이 Interface라면 그 골격 구현 클래스의 이름은 AbstractInterface 짓는다. 컬렉션 프레임워크의 AbstractCollection, AbstractSet, AbstractList, AbstractMap 각각이 핵심 컬렉션 인터페이스의 골격 구현이다. <br/>
Abstract로 사용하는게 하나의 관례 <br/>

```java
static List<Integer> intArrayList(int[] a) {
    Objects.requireNonNull(a);
    
    return new AbstractList<>() {
        @Override public Integer get(int i) {
            return a[i];
        }
        
        @Override public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }
        
        @Override public int size() {
            return a.length;
        }
    };
}
```

위에 예는 int 배열 받아 Integer 리스트 형태로 보여주는 어댑터 <br/>

인터페이스를 구현한 클래스에서 해당 골격 구현을 확장한 private 내부 클래스를 정의 <br/>

시뮬레이트한 다중 상속(simulated multiple inheritance) , 다중 상속의 많은 장점을 제공하는 동시에 단점을 피하게 해줌

```java
import java.util.Map;

public abstract class AbstractMapEntry<K, V> implements Map.Entry<K, V> {
    
    @Override
    public V setValue(V value) {
        throw new UnsupportedOperationException();
    }
    
    @Override
    public boolean equals(Object o) {
        if (o==this) return true;
        if(!(o instanceof Map.Entry)) return false;
        Map.Entry<?, ?> e = (Map.Entry) o;
        return Objects.equals(e.getKey(), getkey()) && Objects.equals(e.getValue(), getValue());
    }
    
    @Override public int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }
    
    @Override public String toString() {
        return getKey() + "=" + getValue();
    }
}
```

