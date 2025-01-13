## 아이템 23 - 태그 달린 클래스보다는 클래스 계층구조를 활용하라

### 태그 달린 클래스와 클래스 계층 구조 활용

태그 달린 클래스의 문제점

태그 필드(enum Type)와 다양한 필드를 섞어서 설계한 클래스이다.

태그 필드를 기반으로 조건문(switch)을 사용해 로직을 처리한다.

단점

-   여러 구현이 한 클래스에 섞여 있어 각 타입별로 필요한 필드와 로직이 혼재되어 코드가 복잡해진다.
-   특정 타입에서만 사용하는 필드가 클래스 전체에 존재해 메모리 낭비와 관리가 어렵다.
-   새로운 타입이 추가되면 switch 문과 관련된 로직을 전부 수정해야 해 유지보수가 어렵다.

### 클래스 계층 구조로 리팩토링

태그 필드를 제거하고, 클래스 계층 구조를 활용해 각 타입별 클래스를 분리한다.

추상 클래스나 인터페이스를 활용해 공통 로직과 메서드를 정의하고, 각 타입에 맞는 세부 구현을 제공한다.

예제 코드

```java
// 추상 클래스 정의
abstract class User {
    abstract boolean order(String info);
}

// 세부 구현 : Customer
class Customer extends User {
    
    @Override
    boolean order(String info) {
        // 고객 전용 로직
        return false;
    }
    
}

// 세부 구현 : DeliveryPerson
class DeliveryPerson extends User {
    
    @Override
    boolean order(String info) {
        // 배달원 전용 로직
        return false;
    }
    
}
```

장점

-   각 타입별로 로직이 분리되어 코드가 더 명확해진다.
-   새로운 타입을 추가할 때 기존 코드를 수정하지 않고 클래스를 추가하면 되므로 확장성이 높아진다.
-   각 클래스에 필요한 필드만 정의하므로 불필요한 필드가 제거된다.
