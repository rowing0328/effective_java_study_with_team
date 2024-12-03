# **Item 1: 생성자 대신 정적 팩터리 메서드를 고려하라**

---

## **핵심 주제**

### **정적 팩토리 메서드의 장점 5가지**
1. **이름을 가질 수 있다**
    - **왜 장점인가?**
    - 어떤 의미로 생성하여 만든 것인지 메서드 이름을 통해 명확하게 파악할 수 있다.
    - 똑같은 생성자로 여러개 생성 필요하면, 정적 팩터리 메서드를 사용을 적극적으로 추천
2. **두번째, 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.**
   - 불편 클래스로 미리 캐싱을 사용한다면 성능을 향상 할 수 있다. 불필요한 객체 생성을 방지할 수 있음. 
   
3. **세 번째, 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다**
예시 코드는 아래와 같다.
```
public class Cat {
   private static Cat cat = null;
   
   private Cat() {}
   public static Cat getInstance() {
      // 초기화
      if(cat == null) {
         cat = new Cat();
      }
      return cat;
   }
}
```
````

public interface Shape {
    // Shape interface methods
}

public class Circle implements Shape {
    // Circle implementation
}

public class ShapeFactory {
    public static Shape newCircle() {
        return new Circle();
    }
}
````

4. **네 번째, 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.**
```
// 부모 타입
interface Shape {
    void draw();
}

// 하위 타입: 원
class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle");
    }
}

// 하위 타입: 사각형
class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Rectangle");
    }
}

// 정적 팩토리 메서드를 가진 클래스
class ShapeFactory {
    public static Shape getShape(String type) {
        if ("circle".equalsIgnoreCase(type)) {
            return new Circle();
        } else if ("rectangle".equalsIgnoreCase(type)) {
            return new Rectangle();
        } else {
            throw new IllegalArgumentException("Unknown shape type");
        }
    }
}

// 클라이언트 코드
public class FactoryMethodExample {
    public static void main(String[] args) {
        Shape shape1 = ShapeFactory.getShape("circle");
        shape1.draw(); // Output: Drawing a Circle

        Shape shape2 = ShapeFactory.getShape("rectangle");
        shape2.draw(); // Output: Drawing a Rectangle
    }
}
```

5. **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.**
   - 이게 왜 장점이되는지 궁금함
   - 테스트 코드나 이런 것들을 작성할 떄 유용할 것 같음 
   - 확실한 반환 타입을 정해주지 않아도 에러가 발생 안됨?

```
public interface Vehicle {
    void drive();
}

public class VehicleFactory {
    public static Vehicle getVehicle() {
        // 반환 타입만 인터페이스로 정의
        return null; // 초기에는 구체적인 구현이 없어도 메서드 작성 가능
    }
}
```
**from** : 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
<br/>
**of** : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
<br/>
**valueOf** : from과 of의 더 자세한 버전
<br/>
**instance 혹은 getInstance**: (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
<br/>
**create 혹은 newInstance** : instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
<br/>
**getType**: getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체의 타입이다.
<br/>
**newType**: newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체 타입이 아니다.
<br/>
**type** : getType과 newType의 간결한 버전