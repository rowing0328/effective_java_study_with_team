## 📖 멤버 클래스는 되로록 static으로 만들라

### 핵심 내용

- 중첩 클래스에는 네 가지가 있으며, 각각의 쓰임이 다르다.

- 메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 길다면 멤버 클래스로 만든다.

- 멤버 클래스의 인스턴스 각각이 바깥 인스턴스를 참조한다면 비정적으로, 그렇지 않으면 정적으로 만들자.

- 중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 클래스나 인터페이스가 이미 있다면 익명 클래스로 만들고, 그렇지 않다면 지역 클래스로 만들자.

## 추가 강의 내용 정리

### Nested class - Member class

Nested 되어 있는 member class가 독립적으로 존재할 수 없으며

바깥 instance 없이는 생성할 수 없아야 한다.

```java
@Data
public class User {
    private String name;
    private Address address;
    @Data
    public class Address {
        String zipcode;
    }
    public String getUserName() {
        // 의미가 있는 메서드는 아님
        // 접근 범위 설명 위해 추가함
        return name;
    }
}
// User user = new User();
// user.new Address();
```

### Nested class - static member class

Nested 되어 있는 member class가 독립적으로 존재할 수 있음.

```java
public class Customer {
    private int age;
    private Address address;
    public String printBarcode(){
        return address.fullAddress+address.zipcode;
    }
    private static class Address {
        private String fullAddress;
        private String zipcode;
    }
}
```

### Nested class - Anonymous class

Nested 되어 있는 member class가 독립적으로 존재할 수 있음.

```java
public interface MyName {
    public int getAge();
}

// ... 실행 code
MyName myName = new MyName() {
    private int age;
    @Override
    public int getAge() {
        return age;
    }
};
```
```java
public abstract class MyName {
    public int getAge() {
        return 0;
    }
}
// OR
public class MyName {
    public int getAge() {
        return 0;
    }
}
// 실행 코드
MyName myName = new MyName(){
    private int age;
    @Override
    public int getAge() {
        return age;
    }
};
```

### Nested class - Local class

- Local variable을 선언할 수 있는 곳에 선언하여 사용

- Ex) method body

```java
public void getName() {
    class Name {
        public int age;
    }
}
```

### 정리

Nested class

- Method 밖에서 사용할 것이다 -> member class
    - 그중 member class가 바깥 instance를 참고한다 -> non-static
    - 그 외 -> static
- 딱 한곳에서 사용 && 사전 struct가 있다 -> Anonymous class
- 그 외 -> local class


## 🤔 생각 정리
- 중첩 클래스를 사용할 때, 주의해야 하는 사항을 한번 더 확인할 수 있는 내용이 있어서 도움이 많이 되었다.
- 그리고 예제를 보면서 쓰림을 다시 한번 눈으로 확인할 수 있어 중첩 클래스를 어떻게 사용하는지 알 수 있었다.

