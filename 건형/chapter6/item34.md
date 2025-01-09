## 📖 int 상수 대신 열거 타입을 사용하라


### 핵심 내용

- 열거 타입은 확실히 정수 상수보다 뛰어나다.

- 더 읽기 쉽고 안전하고 강력하다.

- 대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작하게 할 떄는 필요하다.

- 드물게는 하나의 메서드가 상수별로 다르게 동작해야 할 때도 있다.

- 이런 열거 타입에서는 switch 문 대신 상수별 메서드 구현을 사용하자.

- 열거 타입 상수 일부가 같은 동작을 공유한다면 전략 열거 타입 패턴을 사용하자.


## 💡 주요 내용 정리 & 🛠️ 실습 코드

일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입 -> 열거 타입

열거 타입 지원 이전의 정수 열거 패턴
```java
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_FUJI = 0;
public static final int ORANGE_PIPPIN = 1;
public static final int ORANGE_GRANNY_SMITH = 2;
```
정수 열거 패턴은 타입 안전을 보장할 방법이 없으며 표현력도 좋지 않다.

정수 열거 패턴을 사용한 프로그램은 깨지기 쉽다.

평범한 상수를 나열한 것뿐이라 컴파일하면 그 값이 클라이언트 파일에 그대로 새겨진다.

따라서 상수의 값아 바뀌면 클라이언트도 반드시 다시 컴파일해야 한다.

### 열거 타입

가장 단순한 열거 타입
```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
public enum Orange { NAVEL, TEMPLE, BLOOD }
```

자바 열거 타입은 열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다.

열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final 이다.

따라서 클라이언트가 인스턴스를 직접 생성하거나 확장할 수 없으니 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장된다.


- 열거 타입은 컴파일타임 타입 안전성을 제공한다.

- 열거 타입에는 각자의 이름공간이 있어서 이름이 같은 상수도 평화롭게 공존한다.

- 열거 타입에 새로운 상수를 추가하거나 순서를 바꿔도 다시 컴파일하지 않아도 된다.

- 열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다.

- 열거 타입에는 임의 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현할게 할 수도 있다.

- 열거 타입은 Object 메서드들을 높은 품질로 구현해놨고, Comparable과 Serializable을 구현했으며, 그 직렬화 형태도 왠만큼 변형을 가해도 문제없이 동작하게끔 구현해 놓았다.

- 열거 타입은 가장 단순하게는 그저 상수 모음일 뿐인 열거 타입이지만, (실제로는 클래스이므로) 고차원의 추상 개념 하나를 완벽히 표현해낼 수도 있다.


데이터와 메서드를 갖는 열거 타입
```java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24,6.052e6);
    
    private final double mass; // 질량 (단위 : 킬로그램)
    private final double radius; // 반지름 (단위 : 미터)
    private final double surfaceGravity; // 표면중력 (단위 : m / s^2)
    
    // 중력상수(단위: m^3 / kg s^2)
    private static final double G = 6.67300E-11;

    // 생성자
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }
    
    public double mass() { return mass; }
    public double radius() { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity; // F = ma
    }
}
```

**열거 타입 상수 각각을 특정 데이터와 연결지으려면 생성자에서 데이터를 받아 인스턴스 필드에 저장하면 된다!**

열거 타입은 근본적으로 불변이라 모든 필드는 final이어야 한다. 필드를 public으로 선언해도 되지만, private으로 두고 별도의 public 접근자 메서드를 두는 게 낫다.

**열거 타입은 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는 정적 메서드인 values를 제공한다!**

값들은 선언된 순서로 저장된다!!

각 열거 타입 값의 toString 메서드는 상수 이름을 문자열로 반환하며, 재정의도 가능하다.

널리 쓰이는 열거 타입은 톱레벨 클래스로 만들고, 특정 톱레벨 클래스에서만 쓰인다면 해당 클래스의 멤버 클래스로 만든다.

### 열거 타입은 상수별로 다르게 동작하는 코드를 구현하는 수단을 제공한다.

열거 타입에 apply라는 추상 메서드를 선언하고 각 상수별 클래스 몸체 (constant-specific class body),

즉 각 상수에서 자신에 맞게 재정의하는 방법이다.

이를 **상수별 메서드 구현**이라 한다.

상수별 메서드 구현을 활용한 열거 타입
```java
public enum Operation {
    PLUS {public double apply(double x, double y){return x + y;}},
    MINUS {public double apply(double x, double y){return x - y;}},
    TIMES {public double apply(double x, double y){return x * y;}},
    DIVIDE {public double apply(double x, double y){return x / y;}};
    public abstract double apply(double x, double y);
}
```

apply 메서드가 상수 선언 바로 옆에 붙어 있으니 새로운 상수를 추가할 때 apply도 재정의해야 한다는 사실을 깜빡하기 어렵다.

apply가 추상 메서드이므로 재정의하지 않았다면 컴파일 오류로 알려준다.

### 상수별 메서드 구현을 상수별 데이터와 결합할 수도 있다.
```java
public enum Operation {
    PLUS("+") {public double apply(double x, double y){return x + y;}},
    MINUS("-") {public double apply(double x, double y){return x - y;}},
    TIMES("*") {public double apply(double x, double y){return x * y;}},
    DIVIDE("/") {public double apply(double x, double y){return x / y;}};
    
    private final String symbol;
    
    Operation(String symbol) { this.symbol = symbol; }
    
    @Override public String toString() { return symbol; }
    
    public abstract double apply(double x, double y);
}
```

열거타입에는 상수 이름을 입력받아 그 이름에 해당하는 상수를 반환해주는 valueOf(String) 메서드가 자동 생성된다.

전략 열거 타입 패턴
```java
enum PayrollDay {
    MONDAY(WEEKDAY),
    TUESDAY(WEEKDAY),
    WEDNESDAY(WEEKDAY),
    THURSDAY(WEEKDAY),
    FRIDAY(WEEKDAY),
    SATURDAY(WEEKEND),
    SUNDAY(WEEKEND);
    
    private final PayType payType;
    
    PayrollDay(PayType payType) {
        this.payType = payType;
    }
    
    int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }
    
    // 전략 열거 타입
    enum PayType {
        WEEKDAY {
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked <= MINS_PER_SHIFT ? 0 : (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int mins, int payRate);
        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
```

switch 문을 이용해 원래 열거 타입에 없는 기능을 수행한다.
```java
public static Operation inverse(Operation op) {
    switch(op) {
        case PLUS: return Operation.PLUS;
        case MINUS: return Operation.MINUS;
        case TIMES: return Operation.TIMES;
        case DIVIDE: return Operation.DIVIDE;
    }
    default:
    throw new AssertionError("알 수 없는 연산: " + op);
}
```
추가하려는 메서드가 의미상 열거 타입에 속하지 않는다면 직접 만든 열거 타입이라도 이 방식을 적용하는 게 좋다.

대부분의 경우 열거 타입의 성능은 정수 상수와 별반 다르지 않다.

열거 타입을 메모리에 올리는 공간과 초기화하는 시간이 들긴 하지만 체감될 정도는 아니다.


### 열거 타입을 언제 쓰면 되나요??

필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 항상 열거 타입을 사용하자.

열거 타입에 정의된 상수 개수가 영원히 고정불변일 필요는 없다.

열거 타입은 나중에 상수가 추가돼도 바이너리 수준에서 호환되도록 설계되었다.


## 추가 강의 내용 정리

### 구별을 위해서는 enum을 사용하자

- 책의 말처럼 정수를 열거해 타입을 구분하는건 아무런 이득이 없다.

간단한 형태의 enum
```java
@AllArgsConstructor
@Getter
public enum Fruit {
    APPLE(1000, 20, "RED"),
    PEACH(2000, 9, "YELLO");
    
    private final int price;
    private final int box;
    private final String color;
    public int boxPrice() {
        return price * box;
    }
}
```

- 타입으로 분류될 수 있는 두개 이상의 값이 존재하면 enum을 쓴다.

- 확장 가능성이 있을 경우에는 미리 enum화 시키는 것이 좋다.

- 단일 상속값을 사용할때만 상수를 사용한다.

- 특정 서비스에 종속되어 있지 않으면 enum화 종속되어 있으면 enum화 시키지 않기도 함.


### fromString method (자주 쓰이는 pattern)
```java
private static final Map<String, Fruit> stringToEnum = 
        Stream.of(values())
                .collection(Collectors.toMap(Objects::toString, e -> e));

public static Optional<Fruit> fromString(String symbol) {
    return Optional.ofNullable(stringToEnum.get(symbol));
}
```

타 서버에서 불확실성을 가지고 enum이 넘어오거나

(100% 확신할 수 있으면 바로 enum을 받기도 함)

DB 등의 값을 처리할 때 등등 유용할 수 있다.


### 정리

Enum화 시킬 수 있는 상황이면 가급적리면 하는 것이 좋다!

또한 외부 type의 Symbol이 불안정하다면, fromString을 통한 방안도 고려해 볼만 하다.


## 🤔 생각 정리
- Enum을 사용해야 하는 상황과 기준을 배울 수 있었다.
- 같은 문맥을 갖고있는 상수를 묶어서 함께 관리하면 확장성이 매우 증가할 것 같다!

