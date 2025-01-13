## 아이템 19 - 상속을 고려해 설계하고 문서화하라

### 상속 설계 시 고려할 점

-   상속의 논리적 적합성  
    상속을 허용하려면 부모 클래스의 동작을 상속받아 확장하는 것이 논리적으로 타당해야 한다.
-   내부 구현에 대한 의존 최소화  
    하위 클래스가 부모 클래스의 내부 구현 세부 사항에 의존하지 않도록 설계해야 한다.
-   메서드 동작과 제약의 명확한 문서화  
    부모 클래스의 재정의 가능한 메서드의 동작과 제약 조건을 명확히 문서화하여, 하위 클래스에서 예측 가능한 동작을 구현할 수 있도록 해야 한다.

### 잘못된 상속 설계의 예시

코드 예제

```java
public class Super {
    
    public Super() {
        overrideMe(); // 생성자에서 재정의 가능한 메서드를 호출
    }

    public void overrideMe() {
    }
    
}

public class Sub extends Super {
    
    private final Instant instant;

    public Sub() {
        instant = Instant.now();
    }

    @Override
    public void overrideMe() {
        System.out.println(instant);
    }
    
}
```

설명

Super 클래스의 생성자에서 overrideMe() 메서드를 호출한다.

Sub 클래스는 overrideMe()를 재정의했으며,  이 메서드에서 instant를 참조한다.

그러나 instant가 초기화되기 전에 메서드가 호출되므로, 결과적으로 null이 출력된다.

### 상속을 금지하는 방법

```java
public final class ProhibitInheritance {
}
```

-   클래스를 final로 선언  
    클래스를 final로 선언하면 상속이 불가능하다.  
    이 방법은 간단하면서도 확실하게 상속을 금지할 수 있다.

```java
@Getter
public class ProhibitInheritance {
    
    private int sum;

    private ProhibitInheritance() {
    }

    private ProhibitInheritance(int sum) {
        this.sum = sum;
    }

    public static ProhibitInheritance getInstance() {
        return new ProhibitInheritance();
    }
    
}
```

-   모든 생성자를 private 또는 package-private으로 선언  
    생성자를 private 또는 package-private으로 선언하여 상속을 방지한다.  
    외부에서 인스턴스를 생성하려면 정적 팩토리 메서드를 사용하도록 설계한다.

```java
/**
 * @implSpec
 * 해당 메서드는 전달받은 색상으로 모든 도형을 그립니다.
 */
public void draw(String color) {
    for (Shape shape : shapes) {
        shape.draw(color);
    }
}
```

-   @implSpec 어노테이션 활용  
    @implSpec 어노테이션은 상속 가능한 메서드의 동작을 설명할 때 사용된다.  
    재정의해야 할 메서드의 조건과 제약을 명시함으로써, 상속 설계의 의도를 문서화할 수 있다.

### 상속 설계 시 검증

-   Clone 및 readObject와 같은 재정의 가능한 메서드 호출 피하기  
    이들 메서드는 재정의 가능하므로, 상속 설계 시 직접 호출하지 않도록 주의해야 한다.  
    불필요한 호출은 예측 불가능한 동작을 초래할 수 있다.
-   재정의 가능한 메서드 호출 여부 확인  
    부모 클래스에서 직접 또는 간접적으로 재정의 가능한 메서드를 호출하지 않도록 설계해야 한다.  
    특히, 생성자나 초기화 블록에서 이러한 메서드를 호출하면 예기치 않은 동작이 발생할 수 있다.

### 인터페이스와 컴포지션을 활용한 상속 대안

-   인터페이스를 통한 구현 추천  
    인터페이스는 명확한 계약을 정의하며, 구현에 대한 의존성을 줄인다.
-   컴포지션 (Composition)  
    기능 확장이 필요하면 상속 대신 기존 클래스의 인스턴스를 포함하는 방식을 사용한다.