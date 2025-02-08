## 아이템 49 - 매개변수가 유효한지 검사하라

### 매개변수 검사의 필요성

API나 메소드 호출 시 매개변수(parameter)가 올바른 값인지 검사하는 것은 매우 중요하다.

잘못된 값이 들어올 가능성을 줄이기 위해 사전에 유효성을 검증해야 서비스의 안정성을 확보할 수 있다.

예제 코드 - 장바구니 아이템 추가

```java
@RestController
public class ParameterCheckController {

    @Data
    class AddItemForm {
        private long itemId;
        private int count;
    }

    @PostMapping(value = "/{userId}/add")
    public ResponseEntity getBasketInfo(
        @PathVariable long userId,
        @RequestBody AddItemForm addItemForm
    ) {
        // 매개변수 검사가 필요함
    }
    
}
```

-    이처럼 매개변수를 받는 API에서는 유효성 검사 로직이 포함되지 않으면 예상치 못한 문제가 발생할 수 있다.

### 검사 위치와 TPO(Time, Place, Occasion)의 중요성

매개변수 검사는 메소드의 시작 부분에서 명시적으로 수행하는 것이 좋다.

특히 외부에 공개된 API에서는 반드시 엄격한 검사가 필요한다.

내부 코드에서만 사용된다 하더라도 예상하지 못한 값이 들어오는 상황을 고려해야 한다.

예제 코드

```java
/**
 * 사용자 포인트를 계산하는 메소드
 * @param userId 사용자 ID
 * @param cardId 사용자의 카드 ID
 * @param orderItemInfoVoList 주문 아이템 정보
 * @return 계산된 포인트 값
 */
public static double calculateUserPoint(
    long userId,
    long cardId,
    List<OrderItemInfoVo> orderItemInfoVoList
) {
    // 매개변수에 대한 검사가 필요
}
```

### 검사 기준의 문서화

매개변수 검사를 수행할 때는 규칙을 문서화해 두는 것이 좋다.

실제로 이를 코드에 적용하는 것은 어렵지만, 문서와 테스트 코드를 통해 보완할 수 있다.

검사를 지나치게 강하게 하는 것도 문제지만, 기본적인 검사를 통해 코드 품질을 높이는 것이 중요하다.