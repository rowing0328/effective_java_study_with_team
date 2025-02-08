## 아이템 51 - 메서드 시그니처를 신중히 설계하라

### 메서드 이름의 중요성

메서드 이름은 코드 가독성과 유지보수에 큰 영향을 미친다.

-   표준 명명 규칙을 따르고 너무 긴 이름은 피해야한다.
-   예시로, 아래와 같은 지나치게 긴 메서드 이름은 혼란을 초래할 수 있다.

예제 코드 - Bad Pratice

```java
findByIdAndItemNameAndItemTargetSubIdAndCouponGroupIdOrderByCreatedDateDesc
```

올바른 메서드 명명 규칙 요약

-   메서드 이름은 명확하고 간결하게 작성한다.
-   길어질 경우, 메서드가 지나치게 많은 기능을 수행하고 있는지 점검한다.

### 편의 메서드 남용 방지

Util 클래스나 Helper 메서드는 자주 사용될 때만 제공하는 것이 좋다.

-   불 필요한 편의 메서드가 많아지면 코드 복잡도가 증가하고 유지보수가 어려워질 수 있다.
-   확신이 없다면 메서드를 private로 선언한 후 필요에 따라 점진적으로 리팩토링하는 방식이 권장된다.

### Parameter 목록을 짧게 유지하라

파라미터가 너무 많을 경우 메서드를 이해하기 어렵고 실수할 가능성이 커진다.

특히 같은 타입의 여러 파라미터가 존재할 경우 더 혼란스럽다.

해결 방법

1.  메서드가 너무 장황하다면 분리하거나 구조를 단순화한다.
2.  파라미터를 \*\*Value Object(VO)\*\*로 묶어 사용한다.

예제 코드

```java
class UserVo {
    private long id;
    private int age;
    private int footSize;
    private float weight;
    private float height;
}

// 메서드 호출 예시
public static void expandParam(long id, int age, int footSize, float weight, float height) {}
public static void voParam(UserVo userVo) {}  // 간결해진 형태
```

### Parameter 타입은 Interface로

-   메서드 파라미터로 클래스보다 인터페이스 타입을 사용하는 것이 좋다.

예를 들어, ArrayList<String>이 아닌 List<String>으로 선언하여 유연성을 확보할 수 있다.

예제 코드

```java
public static void example() {
    List<String> aList = new ArrayList<>();
}
```

### Boolean 파라미터는 Enum으로 대체하라

Boolean 같은 참/거짓의 두가지 상태만 표현할 수 있다.

필요에 따라 Enum을 사용하여 의미를 명확히 하는 것이 좋다.

예제 코드

```java
enum SmartPhoneChargeType {
    FIVE_PIN, LIGHTNING, C_TYPE
}

boolean isFivePin;  // 불명확한 표현
```
