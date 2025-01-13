## 아이템 38 - 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

우선, 연산(예: 더하기, 빼기)을 표현하기 위한 Operation 인터페이스를 정의한다.

```java
public interface Operation {
    double apply(double x, double y);
}
```

enum을 사용하여 기본 연산(더하기, 빼기)을 정의할 수 있다.

```java
@AllArgsConstructor
public enum BasicOperation implements Operation {
    
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
   
   MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    };

    private final String symbol;
    
}
```

-   명확한 구조  
    각 연산의 구현을 enum의 각 상수에서 직접 정의할 수 있다.
-   타입 안정성  
    Operation 인터페이스를 구현하여 타입 안전성을 보장한다.

### 확장 가능한 열거 타입

기본 연산 외에 새로운 연산(곱하기, 나누기 등)을 추가하려면 다른 enum에서도 Operation 인터페이스를 구현할 수 있다.

예제 코드

```java
@AllArgsConstructor
public enum ExtendedOperation implements Operation {
    
    MULTIPLY("*") {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;
    
}
```

활용 예시

```java
public static void test(Collection<? extends Operation> opSet, double x, double y) {
    for (Operation op : opSet) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}

// 사용 예시
test(EnumSet.allOf(BasicOperation.class), 2, 3);
test(EnumSet.allOf(ExtendedOperation.class), 2, 3);
```

연산 집합을 받아 테스트하는 메서드를 위와 같이 활용 할 수 있다.

### 실무에서의 활용

enum 자체는 확장이 불가능하므로,

기본 열거 타입만 사용하면 새로운 기능을 추가하기 어렵다.

인터페이스를 통해 여러 enum에서 동일한 동작을 구현할 수 있다.

API가 인터페이스 기반으로 작성되면,

확장된 enum을 자유롭게 추가할 수 있다.
