## 아이템 20 - Abstract class 보다는 interface를 우선하라

### Extends vs Implements

-   extends  
    단일 상속만 가능하다.  
    한 클래스는 하나의 상위 클래스를 상속받을 수 있다.
-   implements  
    다중구현이 가능하다.  
    클래스는 여러 개의 인터페이스를 구현할 수 있다.

### 예제 코드

```java
public class Sub extends Super implements Serializable, Cloneable {}

public interface Sub extends Serializable, Cloneable {}
```

### Default Method와 Interface

Java 8부터 default 메서드가 추가되어 기본 동작을 제공하는 메서드를 인터페이스 내에 정의할 수 있다.

예제 코드

```java
public interface Packable {

    default void packOrder() {
        System.out.println("포장 주문이 들어왔습니다.");
    }
    
}

public class Restaurant implements Delibariable, Packable {
    // Packable의 default 메서드 활용 가능
}
```

### Default Method의 Diamond 문제

인터페이스에 동일한 이름과 시그니처를 가진 default 메서드가 존재하면 다이아몬드 문제가 발생할 수 있다.

이를 방지하기 위해 클래스에서 명시적으로 해당 메서드를 재정의해야 한다.

예제 코드

```java
public class Restaurant implements Delibariable, Packable {
    
    @Override
    public void order() {
        // 명시적으로 메서드를 구현해야 충돌 방지
    }
    
}
```

설명

Delibariable과 Packable 인터페이스에 동일한 이름의 default 메서드가 정의된 경우,  
클래스에서 해당 메서드를 명시적으로 구현하지 않으면 컴파일 오류가 발생한다.

### Skeletal Implementation (추상 골격 구현)

추상 클래스와 인터페이스를 조합하여 설계한다.

Interface 설계의 뼈대를 만든다.

인터페이스는 설계의 뼈대를 정의하며, 기본적으로 메서드의 시그니처만 제공한다.  
필요에 따라 default 메서드를 추가하여 기본 구현을 제공할 수도 있다.

예제 코드

```java
public interface Shape {
    
    double calculateArea(); // 면적 계산
    double calculatePerimeter(); // 둘레 계산

    default void displayType() {
        System.out.println("This is a shape.");
    }
    
}
```

### Abstract Class로 공통 로직 구현

추상 클래스는 인터페이스를 구현하며, 공통으로 사용할 수 있는 메서드를 정의하거나 일부 메서드를 미완성 상태로 유지한다.

예제 코드

```java
public abstract class AbstractShape implements Shape {
    
    protected String name;

    public AbstractShape(String name) {
        this.name = name;
    }

    @Override
    public void displayType() {
        System.out.println("This is a " + name + ".");
    }

    public abstract double calculateArea();
    public abstract double calculatePerimeter();
    
}
```

### Sub Class 세부 구현

구체적인 클래스를 작성하여 추상 클래스를 상속받고 세부적인 로직을 완성한다.

예제 코드

```java
public class Circle extends AbstractShape {

    private double radius;

    public Circle(String name, double radius) {
        super(name);
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }

    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }
    
}

public class Rectangle extends AbstractShape {

    private double width;
    private double height;

    public Rectangle(String name, double width, double height) {
        super(name);
        this.width = width;
        this.height = height;
    }

    @Override
    public double calculateArea() {
        return width * height;
    }

    @Override
    public double calculatePerimeter() {
        return 2 * (width + height);
    }
    
}
```

사용 예시

```java
public class Main {
    
    public static void main(String[] args) {
        Shape circle = new Circle("Circle", 5);
        circle.displayType();
        System.out.println("Area: " + circle.calculateArea());
        System.out.println("Perimeter: " + circle.calculatePerimeter());

        Shape rectangle = new Rectangle("Rectangle", 4, 6);
        rectangle.displayType();
        System.out.println("Area: " + rectangle.calculateArea());
        System.out.println("Perimeter: " + rectangle.calculatePerimeter());
    }
    
}
```

주의사항

Object 클래스의 메서드(equals, toString 등)는 default 메서드로 제공하지 않는 게 좋다.

Object 클래스는 모든 클래스의 상위 클래스라서

인터페이스에서 default 메서드로 제공하면 클래스 계층 구조에서 충돌이 발생할 수 있다.

예를 들어, equals 메서드를 default로 정의하면

하위 클래스에서 이를 재정의하거나 사용할 때

예상치 못한 문제가 생길 수 있다.