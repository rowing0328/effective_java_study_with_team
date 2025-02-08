## 아이템 62 - 다른 타입이 적절하다면 문자열 사용을 피하라

### 문자열 사용의 문제점

문자열을 모든 데이터 표현에 사용하면 구조의 명확성이 떨어지고 오류 발생 가능성이 높아진다.

특히 구조화된 데이터나 열거형 데이터를 문자열로 처리하면, 코드가 복잡해지고 유지보수가 어려워진다.

또한, 문자열로 데이터를 표현하면 형 변환과 값 비교 과정에서 문제가 발생할 수 있다.

오타나 형식 변경과 같은 작은 실수로도 코드가 정상적으로 동작하지 않는 경우가 많아진다.

따라서, 가능한 경우 적절한 데이터 타입을 사용하여 명확성과 안정성을 확보하는 것이 중요하다.

문제 코드

```
String data = "123||BOOK||A book description";
String[] parts = data.split("\\|\\|");

// 잘못된 인덱스로 접근하거나 데이터 형식이 바뀌면 오류 발생 가능
int id = Integer.parseInt(parts[0]);
String type = parts[1];
```

해결 방법

명확한 데이터 타입을 사용하여 구조를 정의한다.

열거형 데이터를 사용할 경우 enum을 활용한다.

enum을 활용한 예시

```
public enum ItemType {
    BOOK, ELECTRONIC, CLOTHING
}

public class Item {

    private int id;
    private ItemType type;
    private String description;

    // 생성자와 getter/setter
    public Item(int id, ItemType type, String description) {
        this.id = id;
        this.type = type;
        this.description = description;
    }

    public ItemType getType() {
        return type;
    }
    
}
```