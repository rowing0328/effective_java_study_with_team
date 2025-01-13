## 아이템 21 - 인터페이스는 구현하는 쪽을 생각해 설계하라

default 메서드는 인터페이스에 메서드 구현을 포함시켜

이를 구현한 클래스에서 재정의하지 않으면 기본 구현이 사용되도록 하는 기능이다.

하지만 모든 구현체와 매끄럽게 연동된다는 보장은 없어서

주의해서 사용해야 한다.

### 디폴트 메서드의 장점과 한계

장점

-   기존 인터페이스에 새로운 메서드를 추가할 수 있어, 하위 호환성을 유지하면서도 기능을 확장할 수 있다.
-   새로운 인터페이스를 설계할 때 표준적인 메서드 구현을 제공하여 인터페이스를 쉽게 구현할 수 있다.

한계

-   모든 구현체의 상황을 고려해 불변식을 해치지 않는 디폴트 메서드를 작성하는 것은 어렵다.
-   구현체의 특수한 요구 사항과 충돌하거나, 예상치 못한 런타임 오류가 발생할 수 있다.

사용 예시

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
numbers.removeIf(n -> n % 2 == 0); // 짝수를 제거
System.out.println(numbers); // 출력: [1, 3, 5]
```

설명

Java 8에서는 Collection 인터페이스에 removeIf 디폴트 메서드가 추가되었다.

이 메서드는 조건(Predicate)에 맞는 요소를 컬렉션에서 제거하는 데 사용된다.

### SynchronizedCollection과의 문제

SynchronizedCollection은 스레드 안전을 보장하기 위해

모든 메서드 호출을 락 객체로 동기화한 뒤, 내부 컬렉션에 작업을 위임하는 래퍼 클래스다.

하지만 removeIf는 default 메서드로 제공되므로

동기화를 고려하지 않고 작성되었다.

이로 인해 removeIf를 사용하면 동기화 요구 사항을 충족하지 못하고,

멀티스레드 환경에서 데이터 손상이 발생할 수 있다.

예제 코드

```java
@Override
public boolean removeIf(Predicate<? super E> filter) {
    
    synchronized (lock) {
        return collection.removeIf(filter);
    }
    
}
```

설명

Java는 이러한 문제를 해결하기 위해, 구현체에서 default 메서드를 재정의할 수 있는 기능을 제공한다.

예를 들어, SynchronizedCollection에서는 removeIf를 재정의하여 동기화를 추가한다.
