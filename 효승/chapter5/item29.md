## 아이템29 - 이왕이면 제네릭 타입으로 만들라

### Type Parameter Naming Conventions

-   E (Element)  
    요소를 나타낼 때 사용한다.
-   K (Key)  
    키를 나타낼 때 사용한다.
-   N (Number)  
    숫자를 나타낼 때 사용한다.
-   T (Type)  
    일반 타입을 나타낼 때 사용한다.
-   V (Value)  
    값을 나타낼 때 사용한다.
-   S, U, V  
    2번째, 3번째, 4번째 타입을 나타낼 때 사용한다.

예제 코드

```java
@NoRepositoryBean
public interface JpaRepository<T, ID> { }
```

설명

-   T  
    엔티티 타입
-   ID  
    엔티티의 고유 식별자 타입

### Generic Type Example

제네릭 타입의 장점을 이해하기 위해 간단한 덧셈 및 뺄셈 계산기를 구현해 보았다.

이 계산기는 제네릭 타입 E를 사용하여 다양한 데이터 타입을 유연하게 처리할 수 있다.

예제 코드

```java
public class Calculator<E> {
    
    private StringBuilder expression;

    public Calculator() {
        expression = new StringBuilder();
    }

    public void add(E e) {
        expression.append("+" + e.toString());
    }

    public void minus(E e) {
        expression.append("-" + e.toString());
    }

    public String expression() {
        if (expression.charAt(0) == '+') {
            return expression.substring(1); // '+' 제거
        } else {
            return expression.toString();
        }
    }
    
}
```

설명

-   제네릭 타입 E  
    타입에 따라 다양한 데이터(Integer, Double 등)를 처리할 수 있다.  
    강력한 타입 안정성과 재사용성 제공한다.
-   동적 표현식 생성  
    add()와 minus() 메서드를 호출하여 수식 표현식을 동적으로 생성한다.  
    타입에 관계없이 표현식을 생성 가능하다.

### 왜 제네릭 타입을 사용해야 하는가

-   타입 안정성 보장  
    Object를 사용하는 경우, 명시적 형변환이 필요해 런타임 에러 가능성이 있다.  
    제네릭을 사용하면 컴파일 시 타입 검증이 이루어져, 타입 안전성을 확보할 수 있다.
-   가독성과 유지보수성 개선  
    명시적 형변환이 없어 코드가 깔끔해지고, 가독성이 향상된다.  
    타입에 대한 제약 조건을 명시적으로 정의할 수 있어, 유지보수성과 확장성이 높아진다.