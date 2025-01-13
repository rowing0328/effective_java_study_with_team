## 아이템 36 - bit field 대신 Enumset을 사용하라

Bit Field는 비트를 이용해 스타일 코드를 나타내는 방식으로, 보통 다음과 같이 구현된다.

예제 코드

```java
public class TextStyleUtil {

    public static final int STYLE_BOLD = 1 << 0;       // 1
    public static final int STYLE_ITALIC = 1 << 1;    // 2
    public static final int STYLE_UNDERLINE = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    public static int getStyleCode(List<String> textStyle) {
        // 스타일 코드를 조합하여 반환
        ...
    }
    
}
```

-   가독성 저하  
    비트 연산을 사용하는 방식은 직관적이지 않아, 코드의 의도를 쉽게 이해하기 어렵다.
-   확장성 부족  
    새로운 스타일을 추가하거나 수정할 때, 기존 비트 값과의 충돌을 방지해야 하며, 이는 관리 비용을 증가시킨다.
-   타입 안정성 부족  
    상수 값들이 int 타입으로 정의되어 있어, 컴파일 시점에 타입 안전성을 보장받기 어렵다.

### EnumSet을 활용한 개선된 구현

EnumSet은 Enum 상수들의 집합(Set)을 표현하기 위한 효율적인 방법이다.

이를 활용하면 Bit Field 방식의 문제점을 해결할 수 있다.

예제 코드

```java
@Getter
@AllArgsConstructor
public enum TextStyle {

    BOLD(1), ITALIC(2), UNDERLINE(4), STRIKETHROUGH(8);

    private final int code;

    public static int getStyleCode(Set<TextStyle> styles) {
        return styles.stream()
                     .mapToInt(TextStyle::getCode)
                     .sum();
    }
    
}

// EnumSet을 활용한 호출 예시
int styleCode = TextStyle.getStyleCode(EnumSet.of(TextStyle.BOLD, TextStyle.ITALIC));
```

-   가독성 향상  
    EnumSet을 사용하면 코드를 보다 직관적으로 읽고 이해할 수 있다.
-   타입 안정성  
    TestStyle이라는 Enum 타입을 강제하여, 컴파일 시점에 타입 오류를 방지한다.
-   효율성  
    EnumSet은 내부적으로 비트 벡터를 사용하므로 매우 빠르고 메모리 사용량도 적다.
-   유지보수 용이성  
    Enum 상수의 추가, 수정, 삭제 작업이 Bit Field보다 훨씬 간단하다.

### 실무에서의 권장 사항

대부분의 상황에서 Bit Field는 사용이 복잡하며,

새로운 코드 작성 및 디버깅 시 어려움을 초래한다.

EnumSet은 Java에서 제공하는 기본 자료구조로,

가독성과 타입 안정성 측면에서 우수하다.

EnumSet은 Enum 상수로만 구성할 수 있으므로,

비 Enum 타입과의 조합이 필요한 경우 별도의 처리가 필요하다.

특정 비트 연산이 필요한 경우,

(예: 시스템 수준의 효율성을 요구하는 작업)에는 여전히 Bit Field를 사용할 수 있다.
