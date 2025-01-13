## 아이템 24 - 멤버 클래스는 되도록 static으로 만들라

### Nested Class - Static Member Class

Static Member Class는 외부 클래스에 종속되지만, 독립적으로 존재할 수 있다.

Static으로 선언된 클래스는 외부 클래스의 인스턴스 없이 사용할 수 있다.

예제 코드

```java
public class Customer {
    
    private int age;
    private Address address;

    public String printBarCode() {
        return address.fullAddress + address.zipcode;
    }

    private static class Address {
        private String fullAddress;
        private String zipcode;
    }
    
}
```

### Nested Class - Member Class

Member Class는 외부 클래스의 인스턴스와 연결되어야만 생성이 가능하다.

독립적으로 사용할 수 없으며, 항상 외부 클래스의 인스턴스를 통해 접근해야 한다.

예제 코드

```java
public class User {
    
    private String name;
    private Address address;

    public class Address {
        private String zipcode;
    }

    public String getUserName() {
        return name;
    }
    
}

// 사용법
User user = new User();
User.Address address = user.new Address();
```

### Nested Class - Anonymous Class

Anonymous Class는 이름이 없는 클래스로, 주로 즉석에서 일회성 객체를 생성할 때 사용된다.

인터페이스 또는 추상 클래스를 구현하여 사용하는 경우가 많다.

예제 코드

```java
public interface MyName {
    int getAge();
}

// 익명 클래스 사용
MyName myName = new MyName() {

    private int age = 25;

    @Override
    public int getAge() {
        return age;
    }
    
};
```

### Nested Class - Local Class

Local Class는 특정 메서드 내에서 정의된 클래스이다.

메서드 내부에서만 사용되며, 해당 메서드가 실행될 때만 접근 가능하다.

예제 코드

```java
public void getName() {
    
    class Name {
        public int age;
    }

    Name name = new Name();
    name.age = 25;
    
}
```