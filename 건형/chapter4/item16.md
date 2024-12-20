# Effective_Java_Study


## 📖 public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

### 핵심 정리

public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. 불변 필드하면 노출해도 덜 위험하지만 완전히 안심할 수는 없다.

하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

```java
// 퇴보한 클래스

class Point {
    public double x;
    public double y;
}
```

이런 클래스는 데이터 필드에 직접 접근할 수 있어, 캡슐화의 이점을 제공하지 못한다.
API를 수정하지 않고는 내부 표현을 바꿀 수 없고, 불변식을 보장할 수 없으며, 외부에서 필드에 접근할 때 부수 작업을 수행할 수도 없다.

```java
// 접근자와 변경자 메서드를 활용한 캡슐화
class Point {
    private double x;
    private double y;
    
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double getX() { return x; }
    
    public double getY() { return y; }
    
    public void setX(double x) { this.x = x; }
    
    public void setY(double y) { this.y = y; }
    
}
```

패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀수 있는 유연성을 얻을 수 있다.

public 클래스가 필드를 공개하면 이를 사용하는 클라이언트가 생겨날 것이므로 내부 표현 방식을 마음대로 바꿀 수 없게 된다.

하지만 package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.



## 추가 강의 내용 정리

### 핵심 내용

public class에서는 get method를 통해 필드에 접근하라

### 캡슐화가 유용한 시나리오 케이스

```java
public class ItemInfo {
    public String name;
    public String price;
}
```

최초에는 name과 price를 response 할 수 있었음

허나 내부 정책이 바뀌어 내부에선 price 대신 cost 라는 용어를 사용하고자 함

밖에는 그대로 price로 노출이 되어야 함.

내부에 어떤 데이터로 관리를 하고 있는지 알 수 없게 한다.

### 캡슐화

조금 더 strict한 규정을 적용하고 싶다면 name의 set을 없앨 수 있다.

```java
public class ItemInfo {
    private String name;
    private int price;
    public ItemInfo(String name, int price) {
        this.name = name;
        this.price = price;
    }
    
    public String getName() {
        return name;
    }
    
    public void setPrice(int price) {
        this.price = price;
    }
    
    public String getPrice() {
        return price;
    }
    
    
}
```
변경이 필요한 필드가 있고 변경되지 않아야 하는 필드가 존재하는 경우
변경되지 않아야 하는 필드는 생성자를 통해서만 초기화 한 후 setter를 구현하지 않고 final 처리하여 불변성을 보장해준다.

### 생각해볼만한 점

캡슐화는 꼭 한번 다시 점검해 보자.
접근제어자는 습관적으로 항상 최소로 사용하자.
