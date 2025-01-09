## 📖 비트 필드 대신 EnumSet을 사용하라

### 핵심 내용

- 열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없다.

- EnumSet 클래스가 비트 필드 수준의 명료함과 성능을 제공하고 아이템 34에서 설명한 열거 타입의 장점까지 선사하기 때문이다.

- EnumSet의 유일한 단점이라면 불변 EnumSet을 만들 수 없다는 것이다.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

```java
public class Text {
    public static final int STYLE_BOLD = 1 << 0; // 1
    public static final int STYLE_ITALIC = 1 << 1; // 2
    public static final int STYLE_UNDERLINE = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    // 매개변수 styles는 0개 이상의 STYLE_ 상수를 비트별 OR한 값이다.
    public void applyStyles(int styles) {
        // ...
    }
}


```

다음과 같은 식으로 비트별 OR를 사용해 여러 상수를 하나의 집합으로 모을 수 있으며, 이렇게 만들어진 집합을 비트 필드라 한다.

text.applyStyles(STYLE_BOLD |STYLE_ITALIC);

비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 수행할 수 있다.

하지만 비트 필드는 정수 열거 상수의 단점을 그대로 지며, 비트 필드 값이 그대로 출력되면 단순한 정수 열거 상수를 출력할 때보다 해석하기 훨씬 어렵다.

그리고, 최대 몇 비트가 필요한지를 API 작성 시 미리 예측하여 적절한 타입을 선택해야 한다.


### EnumSet 클래스는 열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해준다

EnumSet은 Set 인터페이스를 완벽히 구현하며, 타입 안전하고, 다른 어떤 Set 구현체와도 함께 사용할 수 있다.

EnumSet의 내부는 비트 벡터로 구현되었다.

원소가 총 64개 이하라면, 즉 대부분의 경우에 EnumSet 전체를 long 변수 하나로 쵸현하여 비트 필드에 비견되는 성능을 보여준다.

EnumSet - 비트 필드를 대체하는 현대적 기법
```java
public class Text {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
    
    // 어떤 Set을 넘겨도 되나, EnumSet이 가장 좋다.
    public void applyStyles(Set<Style> styles) {
        //...
    }
}
```

text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));


## 추가 강의 내용 정리

대부분의 상황에서 비트 필드를 사용할 이유가 없다.

## 🤔 생각 정리
- 비트 관련 학습 필요

비트가 독립적으로 유지되는 이유는 각 상수가 2의 제곱수로 설정되어 서로 다른 비트 위치를 차지하기 때문입니다. 이로 인해 여러 상수를 비트 OR(|) 연산으로 합쳐도 각 비트가 서로 간섭하지 않고 독립적으로 작동합니다.

상세 원리
각 상수는 서로 다른 비트를 담당:

STYLE_BOLD: 0001 (1)

STYLE_ITALIC: 0010 (2)

STYLE_UNDERLINE: 0100 (4)

STYLE_STRIKETHROUGH: 1000 (8)

각 값은 이진수로 표현했을 때 단일 비트만 1로 설정되어 있습니다. 예를 들어:

STYLE_BOLD는 첫 번째 비트,
STYLE_ITALIC는 두 번째 비트,
STYLE_UNDERLINE는 세 번째 비트를 담당합니다.
비트 OR(|) 연산:

비트 OR 연산은 각 자리의 비트 값을 비교하여 둘 중 하나라도 1이면 결과 비트를 1로 설정합니다.

예: 1 | 0 = 1, 0 | 0 = 0, 1 | 1 = 1
예를 들어, STYLE_BOLD | STYLE_ITALIC의 계산 과정을 보면:

text
코드 복사
STYLE_BOLD:    0001
STYLE_ITALIC:  0010
-----------------
OR 결과:       0011
결과값 0011은 STYLE_BOLD와 STYLE_ITALIC이 동시에 설정되었음을 나타냅니다.

독립성이 유지되는 이유:

각 상수는 다른 비트 위치를 사용하므로 겹치는 부분이 없습니다.
따라서, 여러 값을 합쳐도 각 비트가 독립적으로 설정되고 다른 비트를 변경하지 않습니다.
비트 AND(&)로 특정 비트 확인:

특정 비트가 설정되었는지 확인하려면 비트 AND 연산을 사용합니다.
예: styles & STYLE_BOLD

text
코드 복사
styles:      0011  (STYLE_BOLD | STYLE_ITALIC)
STYLE_BOLD:  0001
-----------------
AND 결과:    0001 (STYLE_BOLD이 설정되어 있음)
반대로, 설정되지 않은 스타일을 확인할 경우:

text
코드 복사
styles:            0011
STYLE_UNDERLINE:   0100
------------------------
AND 결과:          0000 (STYLE_UNDERLINE이 설정되지 않음)

