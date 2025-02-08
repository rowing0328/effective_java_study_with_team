## 아이템 61 - 박싱 타입 대신 기본 타입을 사용하라

### 박싱 타입과 기본 타입의 차이

-   기본 타입 (Primitive Type)  
    int, double, boolean 등과 같은 타입이며,  
    성능이 우수하고 메모리 사용량이 적다.
-   박싱 타입 (Boxed Type)  
    Integer, Double, Boolean 등 기본 타입을 객체로 감싼 타입이며,  
    참조 타입이기 때문에 메모리 사용이 더 크고, 성능이 떨어질 수 있다.

### 박싱 타입의 문제점

-   참조 비교와 값 비교의 혼동  
    박싱 타입은 객체이기 때문에 == 연산자가 참조를 비교한다.  
    값 비교 시 equals() 메서드를 사용해야 한다.

예제 코드

```
Integer a = new Integer(1);
Integer b = new Integer(1);

System.out.println(a == b);       // false (참조가 다름)
System.out.println(a.equals(b));  // true (값이 같음)
```

-   불필요한 메모리 할당  
    박싱 타입은 객체를 생성하므로 기본 타입보다 메모리 사용량이 증가한다.

### 권장 사항

가능한 경우 기본 타입을 사용하여 성능과 메모리 효율성을 높인다.

박싱 타입이 필요할 때는 객체를 직접 생성하지 말고, valueOf() 와 같은 정적 팩토리 메서드를 사용한다.

예제 코드

```
Integer a = Integer.valueOf(1);  // 객체 풀에서 재사용
Integer b = Integer.valueOf(1);

System.out.println(a == b);       // true (같은 객체 참조)
```