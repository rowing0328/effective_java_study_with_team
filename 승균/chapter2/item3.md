## **item3 : private 생성자나 열거 타입으로 싱글턴임을 보증하라**

## **핵심정리**
1. 싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다. => 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트가 테스트하기 어려울 수 있음.

**public static final 필드 방식의 싱글턴**
```java
package darkoverload;

public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {}

    public void leaveTheBuilding() {}
}
```
- 절대로 다른 객체를 참조 할 수 없음. 
- 직관적인거 같아서 좋다. 

**정적 팩터리 방식의 싱글턴**
````java
package darkoverload;

public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() { return INSTANCE; }
    public void leaveTheBuilding() {}
}
````
- API를 바꾸지 않고 싱글턴아니게 변경 가능 
- 팩터리 메서드가 스레드 별로 다른 인스턴스를 넘겨주게 할 수 있음. 
- 정적 팩터리의 메소드 참조를 공급자로 사용할 수 있다.
  Supplier<Elvis> elvisSupplier = Elvis::getInstance;

2. 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법임. 
```java
public enum Elvis {
    INSTANCE;
    public void leaveTheBuilding() { ... }
}
```