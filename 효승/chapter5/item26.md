## 아이템 26 - 로 타입은 사용하지 말라

### Raw 타입이란

제네릭이 도입되기 이전의 컬렉션 클래스와 같이 타입 매개변수를 지정하지 않은 상태의 타입을 의미한다.

예제 코드

```java
List test = new ArrayList<>();
test.add("String");
test.add(1);  // 서로 다른 타입의 객체 추가 허용
```

설명

위 코드는 컴파일러가 타입 안전성을 보장하지 못하기 때문에

런타임 에러가 발생할 가능성이 크다.

예를 들어, String과 Integer 같은 서로 다른 타입의 데이터를

하나의 리스트에 추가해도 컴파일 에러는 발생하지 않는다.

하지만, 해당 리스트를 사용하는 코드에서

예상치 못한 문제가 발생할 수 있다.

### Raw 타입 사용의 문제

-   컴파일 시점  
    컴파일러가 타입 검사(Type Check)를 하지 않으므로 잘못된 데이터 삽입 가능하다.
-   런타임 시점  
    프로그램이 실행되는 동안 타입 불일치로 인해 ClassCastException 같은 에러가 발생할 위험이 있다.

### Unbounded Wildcard Type

Raw 타입 대신 Unbounded Wildcard Type(List<?>)을 사용할 수 있다.

이 타입은 읽기 전용으로 적합하며, 쓰기 작업은 제한적이다.

예제 코드

```java
private int add(List<?> list) {
    list.add(1); // 컴파일 에러 : Unbounded Wildcard는 읽기 전용으로 사용
    return 1;
}
```

설명

List<?>는 타입 안전성을 보장하지만, 내부에 값을 추가하는 등의 쓰기 작업에는 적합하지 않다.

### 왜 Unbounded Wildcard를 사용할까

-   타입 안전성  
    Raw 타입과 달리 List<?>는 컴파일러가 타입 검사를 수행한다.
-   읽기 전용  
    리스트의 데이터를 읽어오는 작업에서는 유용하다.

### 명확한 타입 추론의 필요성

제네릭을 사용하지 않으면 타입 추론이 불가능해지고, 결과적으로 런타임 에러가 발생할 수 있다.

잘못된 예제 코드

```java
static List listMerge(List a, List b) {
    List c = new ArrayList();
    c.addAll(a);  // 타입 정보가 없어 문제 발생 가능
    c.addAll(b);
    return c;
}
```

설명

위 코드는 타입 정보를 명시하지 않아 런타임 에러가 발생할 가능성이 크다.

List의 타입 매개변수를 지정하지 않으면 컴파일러가 타입 충돌을 탐지할 수 없다.

올바른 예제 코드

```java
static <T> List<T> listMerge(List<T> a, List<T> b) {
    List<T> c = new ArrayList<>();
    c.addAll(a);
    c.addAll(b);
    return c;
}
```

설명

제네릭을 사용하여 타입을 명시하면 컴파일러가 타입 충돌을 탐지할 수 있어 더 안전한다.

예외적인 Raw 타입 사용

모든 경우에 Raw 타입이 금지되는 것은 아니다.

일부 상황에서는 Raw 타입이 필요할 수 있다.

예제 코드

```java
# Class 리터럴
List.class;

# 런타임 시 제네릭 타입 소거(Erasure) 처리
if (a instanceof Set) {
    Set<?> set = (Set<?>) a;
    for (Object o : set) {
        if (o instanceof String) {
            // 처리
        }
    }
}
```

설명

이 경우, Raw 타입을 사용하지 않고는 제네릭 정보를 소거한 객체를 처리하기 어렵다.

### Generic Class / Interface

Java의 컬렉션 클래스는 제네릭을 사용하도록 설계되어 있다.

제네릭을 사용하면 타입 안정성을 높이고 재사용성을 극대화할 수 있다.

사용 예시

```java
// 제네릭을 사용한 클래스 선언
List<String> test = new ArrayList<>();

// Generic Interface
public interface List<E> extends Collection<E> { ... }

// Generic Class
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable { ... }
```