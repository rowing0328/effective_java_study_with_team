## 아이템 25 - 톱레벨 클래스는 한 파일에 하나만 담으라

소스 파일 하나에 여러 개의 톱 레벨 클래스를 선언하는 것은 득이 없고 위험하다.

이 방식은 클래스 정의가 중복될 수 있는 위험을 감수하게 하고, 컴파일 순서에 따라 동작이 달라질 수 있다.

### 컴파일 순서에 따라 결과가 달라지는 경우

예제 코드

```java
// Utensil.java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}

// Dessert.java
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}

// Main.java
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
```

실행 결과

-   javac Main.java Utensil.java → pancake
-   javac Dessert.java Main.java → potpie

이처럼 컴파일 순서에 따라 예상치 못한 결과를 초래한다.

### 정적 멤버 클래스로 변환

톱레벨 클래스를 유지할 필요가 없고, 부차적인 관계라면 정적 멤버 클래스를 사용하는 것이 더 적합하다.

예제 코드

```java
public class Main {

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