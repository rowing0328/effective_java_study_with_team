## 📖 태그 달린 클래스보다는 클래스 계층구조를 활용하라

### 핵심 내용

- 태그 달린 클래스를 써야 하는 상황은 거의 없다.

- 새로운 클래스를 작성하는 데 태그 필드가 등장한다면 태그를 없애고 계층구조로 대체하는 방법을 생각해보자.

- 기존 클래스가 태그 필드를 사용하고 있다면 계층구조로 리팩터링하는 걸 고민해보자.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
    
    // 태그 필드 - 현재 모양을 나타낸다.
    final Shape shape;
    
    // 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
    double radius;

    // 원용 생성자
    Figure(double radius) {
        shape = Shape.RECTANGLE;
        this.radius = radius;
    }
    
    // 사각형용 생성자
    Figure(double length, double width) {
        shape = Shapte.RECTANGLE;
        this.length = length;
        this.width = width;
    }
    
    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```

### 태그 달린 클래스의 단점!

- 열거 타입 선언, 태그 필드, switch 문 등 쓸데없는 코드가 많다.
- 여러 구현이 한 클래스에 혼합돼 있어서 가독성도 나쁘다.
- 다른 의미를 위한 코드도 언제나 함께 하니 메모리도 많이 사용한다.
- 필드들을 final로 선언하려면 해당 의미에 쓰이지 않는 필드들까지 생성자에서 초기화해야 한다 (쓰지 않는 필드를 초기화하는 불필요한 코드가 늘어난다.)
- 새로운 의미를 추가할 때마다 모든 switch 문 등 새 의미를 처리하는 코드를 추가해야 하며, 하나라도 빠뜨리면 런타임에 문제가 발생할 수 있다.
- 인스턴스 타입만으로는 현재 나타내는 의미를 알 길이 전혀 없다.

**태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.**

자바와 같은 객체 지향 언어는 타입 하나로 다양한 의미의 객체를 표현하는 훨씬 나은 수단을 제공 : 서브타이핑 (subtyping)


### 태그 달린 클래스를 클래스 계층구조로 바꾸는 방법

1. 계층 구조의 루트(root)가 될 추상 클래스를 정의, 태그 값에 따라 동작이 달라지는 메서드는 루트 클래스의 추상 메서드로 선언
2. 태그 값에 상관없이 동작이 일정한 메서드들을 루트 클래스에 일반 메서드로 추가한다.
3. 모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 전부 루트 클래스로 올린다.
4. 루트 클래스를 확장한 구체 클래스를 의미별로 하나씩 정의한다.

```java
abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;
    
    Circle(double radius) { this.radius = radius; }
    
    @Override double area() { return Math.PI * (radius * radius); }
    
}

class Rectangle extends Figure {
    final double length;
    final double width;
    
    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
    
    @Override double area() { return length * width; }
}
```

## 추가 강의 내용 정리

클래스 계층 구조를 만들기 위한 예제 코드 (tag)
```java
public class User {
    enum Type {RESTAURANT, CUSTOMER, DELIVERY_PERSON}
    final Type type;
    // 음식점과 고객만 필요
    String location;
    // 배달원의 위치
    double latitude;
    double longitude;
    boolean order(String info) {
        // 배달원일 경우 가까운 위치로 주문 설정
        // 레스토랑일 경우 주문 자동 수락
        // 고객일 경우 카드로 결제 후 주문 수락
    }
}
```

### Class 계층 구조로 변환
```java
abstract class User {
    abstract boolean order(String info);
}
class Customer extends User{
    @Override
    boolean order(String info) {
        // 카드로 결제 후 주문 수락
        return false;
    }
}
class DeliveryPerson extends User {
    @Override
    boolean order(String info) {
        // 배달 수락 버튼 누를 시 승락
        return false;
    }
}
```

### 정리

태그 달린 클래스를 써야 하는 상황은 거의 없다.

혹시나 이미 오래된 레거시 시스템 등의 이유를 통해 태그 달린 클래스가 먼저 떠오른다면, 계층 구조로 대체하는 방법을 생각해보자.

이미 태그가 달려 있다면, 계층 구조로 리펙토링 하는 것을 고려해보자.


## 🤔 생각 정리
- 태그 달린 클래스를 쓰는 상황이 있을지는 모르겠지만, 레거시 코드에서 해당 내용이 등장한다면 리펙토링을 고려하는 것이 좋을 것 같다.

