## 태그 달린 클래스보다는 클래스 계층구조를 활용하라

## **핵심정리**
두 가지 이상의 의미를 표현할 수 있으며, 현재 표현하는 의미를 태그 값으로 알려주는 클래스

```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE }
    
    // 태그 필드 - 현재 모양을 나타낸다.
    final Shape shape;
    
    // 다음 필드들은 모양이 사각형(RECTANGLE)
    double length;
    double width;
    
    // 다음 필드는 모양이 원(CIRCLE)일 때만 쓰임
    double radius;
    
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }
    
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
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

한 클래스에 너무 많은 것들이 혼합되어 있어 가독성이 나쁘다. 다른 의미를 위한 코드도 함께 사용하니 쓰이지 않는 필드까지 사용하기에 메모리도 많이 사용함.

또 다른 의미를 추가하려면 코드를 대폭 수정해야함.

**태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.**

태그 달린 클래스는 계층구조를 어슬프게 흉내낸 아류이다.

태그 달린 클래스를 계층구조로 바꾸는 방법 
1. 가장 먼저 계층의 구조(root)가 될 추상 클래스를 정의, 태그 값에 따라 동작이 달라지는 메서드들은 루트 클래스의 추상메서드로 선언
2. 태그 값에 상관없이 동작이 일정한 메서드들은 루트 클래스에 일반메서드로 추가
3. 모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 루트 클래스로 올린다.
4. 루트 클래스를 확장한 구체 클래스를 의미별로 하나씩 정의한다.

**태그 달린 클래스를 클래스 계층구조로 변환**
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
        this.widht = width;
    }
    
    @Override double area() { return length * width; }
}
```

위 구조는 태그 달린 클래스의 단점을 모두 날려버림 <br/>
간결하고 명확하며, 쓸데없는 코드는 모두 사라짐

```java
class Square extends Rectangle {
    Square(double side) {
        super(side, side);
    }
}
```