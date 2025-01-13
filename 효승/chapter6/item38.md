## 아이템 34 - int 상수 대신 enum을 사용하라

### 왜 enum을 사용해야 할까?

다음과 같은 방식으로 상수를 정의해 사용하는 코드는 흔히 볼 수 있다.

```
public static final int APPLE = 0;
public static final int ORANGE = 1;
public static final int BANANA = 2;
public static final int GRAPE = 3;
```

하지만 이 방식은 여러 문제를 야기한다.

```
public static final int FRUIT_APPLE = 10;
public static final int FOOD_APPLE = 10;
```

-   구분의 모호성  
    과일과 음식으로서의 "사과"를 구분해야 할 경우, 값이 중복될 가능성이 있다.
-   타입 안정성 부족  
    int는 단순한 숫자일 뿐, 그 의미를 강제할 수 없다. 이는 코드 리뷰와 디버깅 시 혼란을 야기할 수 있다.

### enum을 사용하여 문제 해결하기

```java
@Getter
@AllArgsConstructor
public enum Fruit {
    
    APPLE(1000, 20, "RED"),
    PEACH(2000, 9, "YELLOW");

    private final int price;
    private final int box;
    private final String color;

    public int boxPrice() {
        return price * box;
    }
    
}
```

-   명확한 타입  
    Fruit이라는 타입을 강제할 수 있어, 컴파일 시점에 오류를 잡을 수 있다.
-   캡슐화된 데이터  
    가격(price), 박스 수량(box), 색상(color) 등을 한곳에 정의할 수 있다.
-   추가 메서드 구현 가능  
    boxPrice()처럼 데이터를 활용한 메서드를 추가하여 객체 지향적인 접근을 할 수 있다.

### 실무에서 사용하는 fromString 패턴

실제 개발에서는 외부에서 전달된 문자열을 enum 값으로 변환해야 하는 경우가 자주 발생한다.

이를 해결하기 위해 fromString 메서드 패턴을 사용할 수 있다.

예제 코드

```java
private static final Map<String, Fruit> stringToEnum = 
    Stream.of(values())
          .collect(Collectors.toMap(Objects::toString, e -> e));

public static Optional<Fruit> fromString(String symbol) {
    return Optional.ofNullable(stringToEnum.get(symbol));
}
```

활용 사례

-   불확실한 입력 처리  
    외부 서버나 데이터베이스에서 불확실한 값이 넘어올 경우, Optional을 사용하여 처리할 수 있다.
-   DB 값 매핑  
    문자열을 enum으로 변환하여 안전하게 사용할 수 있다.
