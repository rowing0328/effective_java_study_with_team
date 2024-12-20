## 아이템 16 - public class에서는 get 메서드를 통해 필드에 접근하라

### 캡슐화의 유용한 사례

초기에는 public String name; 처럼 데이터를 노출하더라도, 이후 내부 정책이 변경되면 큰 문제가 발생할 수 있다.

예를 들어, 내부에서 price 대신 cost라는 용어를 사용하고 싶어질 경우, 외부는 여전히 price로 접근해야 할 수도 있다.

캡슐화를 통해 외부는 내부 데이터의 관리 방식을 알 필요가 없으며, 이는 유연한 설계를 가능하게 한다.

### 캡슐화 구현

`예제 코드`

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

    public int getPrice() {
        return price;
    }
    
}
```

`설명`

필드는 private으로 선언하고, 값에 접근하거나 수정할 수 있도록 getter와 setter 메서드를 제공한다.

특정 필드는 읽기 전용으로 설정할 수 있으며, 이를 위해 getter만 제공하고 setter를 생략하여 데이터의 불변성을 보장한다.

예를 들어, name은 변경할 수 없고, price는 수정 가능하도록 설계할 수 있다.