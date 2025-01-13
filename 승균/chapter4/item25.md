## 톱레벨 클래스는 한 파일에 하나만 담으라

## 핵심 정리

소스 파일 하나에 톱레벨 클래스를 여러 개 선언하더라도 자바 컴파일러는 불평하지 않음 but 아무런 득 없고 위험만 감수

**두 클래스가 한 파일(Utensil.java)에 정의되었다. -따라하지 말 것!**

```java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}
```

**두 클래스가 한 파일(Dessert.java)에 정의되었다. - 따라 하지 말 것!**
```java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "pie";
}
```

단순히 톱레벨 클래스들(Utensil과Desert)을 서로 다른 소스 파일로 분리하면 됨

여러 톱 레벨 클래스를 한 파일에 담고 싶다면 정적 멤버 클래스를 사용하는 방법을 고민해볼 수 있다.

다른 클래스에 딸린 부차적인 클래스라면 정적 멤버 클래스로 만드는게 일반적으로 훨씬 좋음

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
    
    private static class Utensil {
        static final String NAME = "pan";
    }
    
    private static class Dessert {
        static final String NAME = "cake";
    }
}
```