## 아이템 30 - 이왕이면 제네릭 메서드로 만들라

### 제네릭 메서드란

제네릭 메서드는 타입 매개변수를 사용하여 컴파일 시 타입 안정성을 보장하는 메서드이다.  
이는 형변환(casting) 없이도 다양한 타입에 대해 안전하고 유연하게 동작할 수 있게 한다.

### Type-safe Generic Method의 구현

예제 코드

```java
List<String> stringList = List.of("T1", "T2", "T3");

// 제네릭 메서드 예시
static <E> List<E> of(E e1, E e2, E e3) {
    return new ImmutableCollections.ListN<>(e1, e2, e3);
}

// UnaryOperator 예제
private static UnaryOperator<Object> IDENTIFY_FN = (t) -> t;

public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTIFY_FN; 
    // Object는 제네릭으로 캐스팅되지 않으므로 주의 필요
}
```

설명

IDENTIFY\_FN은 Object 타입으로 정의되어 있으나, 내부적으로 타입 캐스팅을 수행한다.

이처럼 타입 안정성을 보장하려면 메서드를 제네릭으로 만들어야 한다.

### Type 제한

제네릭 메서드는 때로 타입 제한(Bounded Type Parameter)을 통해 더 구체적인 타입 제약을 추가할 수 있다.

예제 코드

```java
interface Comparable<T> {
    int compare(T o);
}
```

설명

이처럼 메서드의 파라미터 타입을 특정 인터페이스나 클래스 타입으로 제한하면, 보다 안전한 코드 작성을 할 수 있다.