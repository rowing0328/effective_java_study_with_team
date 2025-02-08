## 아이템 53 - 가변인수는 신중히 사용하라

### 가변 인수의 문제점

Java에서 가변인수(varargs)는 인수의 개수가 달라질 수 있는 상황에서 유용하게 사용된다.

그러나 잘못 사용하면 코드의 안전성과 명확성을 해칠 수 있다.

예를 들어, 필수 인수가 있는 경우에도 가변 인수로만 메서드를 구성하면 호출자가 실수할 여지가 생긴다.

잘못된 예시

```java
static int min(int... args) {
    int min = Integer.MAX_VALUE;
    for (int arg : args) {
        if (arg < min)
            min = arg;
    }
    return min;
}
```

-   위 메서드는 인수를 0개로 호출해도 컴파일 에러가 발생하지 않지만, 실행 시 빈 배열로 인해 잘못된 결과를 반환할 수 있다.

### 가변 인수와 필수 인수를 분리하라

필수 인수는 반드시 가변인수 외부에 명시적으로 선언하여, 호출자가 실수하지 않도록 해야 한다.

올바른 예시

```java
static int min(int firstArg, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs) {
        if (arg < min)
            min = arg;
    }
    return min;
}
```

-   첫 번째 인수를 필수로 지정했기 때문에 최소한 하나의 인수가 반드시 전달된다.
-   호출 시점에 명확한 사용법을 제공하며, 코드 안정성이 향상된다.

### 프레임워크에서의 가변인수 사용 예시

Java의 List.of() 메서드는 가변인수를 활용하되, 다양한 오버로드를 통해 유연성을 제공한다.

다음과 같은 메서드 시그니처들이 제공된다.

예제 코드

```java
List.of();
List.of(E e1);
List.of(E e1, E e2);
List.of(E e1, E e2, E e3, ...);
```

-   이처럼 필요한 경우 적절한 메서드 오버로드를 함께 사용하여 가변인수의 사용을 보완할 수 있다.
