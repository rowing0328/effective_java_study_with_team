## 아이템 35 - ordinal method 대신 instant field를 사용하라

### ordinal() 메서드란

Enum 상수가 정의된 순서를 정수 값으로 반환하는 메서드이다.

예제 코드

```
public enum Ensemble {
    SOLO, DUEL, TRIO, QUARTET;
}
```

위 코드에서 SOLO.ordinal()은 0, DUEL.ordinal()은 1을 반환한다.

-   의미 없는 값 반환  
    ordinal()이 반환하는 값은 단순히 Enum 상수의 선언 순서일 뿐,  
    비즈니스 로직에서 사용하기에 적합하지 않다.
-   유지보수의 어려움  
    Enum에 새로운 상수를 추가하거나 순서를 변경할 경우,  
    기존 코드가 잘못된 값을 참조하게 될 위험이 있다.
-   예상치 못한 오류 발생  
    특정 패턴(예: 1, 2, 4와 같은 비트 연산)에 맞게 구현된 경우,  
    ordinal() 값으로 인해 오류가 발생할 수 있다.

### Instant Field로 문제 해결하기

Enum 내에 상수와 연결된 고유한 값을 저장하기 위해 사용된다.

예제 코드

```java
@AllArgsConstructor
public enum Ensemble {
    
    SOLO(1), DUEL(2), TRIO(3), QUARTET(4), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;

    public int numberOfMusicians() {
        return numberOfMusicians;
    }
    
}
```

-   명확한 값  
    각 상수와 연관된 고유한 값을 정의할 수 있어, 의미를 명확히 전달할 수 있다.
-   확장성과 안정성  
    Enum의 순서를 변경하거나 새로운 상수를 추가하더라도 기존 로직에 영향을 주지 않는다.
-   객체지향적인 접근  
    Enum 내에서 특정 필드와 메서드를 활용하여 보다 구조화된 데이터를 관리할 수 있다.

### 실무에서의 권장 사항

ordinal은 실질적으로 쓸모없는 값이며, 순서를 기반으로 한 논리는 매우 취약하다.

Instant Field를 통해 비즈니스 로직에서 사용할 명확한 값을 Enum에 저장하는 것이 매우 좋다.
