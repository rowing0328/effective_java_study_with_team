## 📖 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

### 핵심 내용

- 열거 타입 자체는 확장할 수 없지만, **인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다.**

- 이렇게 하면 클라이언트는 이 인터페이스를 구현해 자신만의 열거 타입(혹은 다른 타입)을 만들 수 있다.

- 그리고 API가 (기본 열거 타입을 직접 명시하지 않고) 인터페이스 기반으로 작성되었다면

- 기본 열거 타입의 인스턴스가 쓰이는 모든 곳을 새로 확장한 열거 타입의 인스턴스로 대체해 사용할 수 있다.


## 💡 주요 내용 정리 & 🛠️ 실습 코드

열거 타입은 거의 모든 상황에서 타입 안전 열거 패턴보다 우수하다.

단, 예외가 있다!

타입 안전 열거 패턴은 확장할 수 있으나 열거 타입은 그럴 수 없다는 점이다.

타입 안전 열거 패턴은 열거한 값들을 그대로 가져온 다음 값을 더 추가하여 다른 목적으로 쓸 수 있는 반면,

열거 타입은 그렇게 할 수 없다는 뜻이다.


### 열거 타입을 이용해 API가 제공하는 기본 연산 외에 사용자 확장 연산을 추가할 수 있도록 하는 방법

인터페이스를 이용해 확장 가능 열거 타입을 흉내 냈다.
```java
public interface Operation {
    double apply(double x, double y);
}

public enum BasicOperation implements Operation {
    PLUS("+") { public double apply(double x, double y) { return x + y; }},
    MINUS("-") { public double apply(double x, double y) { return x - y; }},
    TIMES("*") { public double apply(double x, double y) { return x * y; }},
    DIVIDE("/") { public double apply(double x, double y) { return x / y; }};
    
    private final String symbol;
    
    BasicOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override public String toString() {
        return symbol;
    }
}
```
열거 타입인 BasicOperation은 확장할 수 없지만 인터페이스인 Operation은 확장할 수 있고, 이 인터페이스를 연산의 타입으로 사용하면 된다.

이렇게 하면 Operation을 구현한 또 다른 열거 타입을 정의해 기본 타입인 BasicOperation을 대체할 수 있다.

확장 가능 열거 타입
```java
public enum ExtendedOperation implements Operation {
    EXP("^") { public double apply(double x, double y) { return Math.pow(x, y);}},
    REMINDER("%") { public double apply(double x, double y) { return x % y; }};
    
    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override public String toString() {
        return symbol;
    }
}
```

새로 작성한 연산은 기존 연산을 쓰던 곳이면 어디든 쓸 수 있다.

BasicOperation이 아닌 Operation 인터페이스를 사용하도록 작성되어 있기만 하면 된다.

apply가 인터페이스(Operation)에 선언되어 있으니 열거 타입에 따로 추상 메서드로 선언하지 않아도 된다.


## 추가 강의 내용 정리

enum을 확장할 수는 없지만, interface를 이용해 같은 효과를 낼 수 있다.

Api가 interface 기반으로 작성되었으면, 확장한 enum의 interface로 대체해 사용할 수 있다.

잘 쓰이지는 않긴 하지만, 꼼꼼히 챕터를 다시 한번 정독 하기를 추천한다.


## 🤔 생각 정리
- 궁금한 점, 논의하고 싶은 내용

