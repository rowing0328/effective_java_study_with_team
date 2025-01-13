## 아이템 31 - 한정적 와일드카드를 사용해 API 유연성을 높여라

Bounded Wildcard란

제네릭 타입의 범위를 제한하여 타입 안전성을 유지하면서도 더 유연한 API를 설계할 수 있도록 돕는 기능이다.

예제 코드

```
public class Stack<E> {
    
    public static final int DEFAULT_SIZE = 20;
    private int size;
    private E[] elements;

    public Stack() {
        elements = (E[]) new Object[DEFAULT_SIZE];
        size = 0;
    }

    public E push(E item) {
        elements[++size] = item;
        return item;
    }

    public void pushAll(Iterable<E> src) {
        for (E e : src) {
            push(e);
        }
    }
    
}
```

문제점

pushAll(Iterable<E> src)는 E 타입에 고정되어 있어,

Stack<Number>에 Iterable<Integer>Iterable <Integer>를 넘기면 컴파일 오류가 발생한다.

이는 제네릭 타입이 불공변성(Invariance)을 가지기 때문이다.

Bounded Wildcard를 활용한 유연한 제네릭 설계

코드 수정

```
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}

// 사용 예시
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> integers = List.of(1, 2);
numberStack.pushAll(integers); // 컴파일 가능
```

설명

-   <? extends E>  
    E를 상속하거나 구현한 모든 타입을 받을 수 있다.  
    상한 경계(Upper Bound)를 설정하여 API 유연성을 높인다.

popAll 메서드와 하한 경계 사용

코드 수정

```
public void popAll(Collection<? super E> dst) {
    while (size > 0) {
        dst.add(elements[--size]);
    }
}

// 사용 예시
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = new ArrayList<>();
numberStack.popAll(objects); // 컴파일 가능
```

설명

-   <? super E>  
    E 타입 또는 그 상위(super) 타입을 받는다.  
    하한 경계(Lower Bound)를 설정하여 데이터를 안전하게 출력한다.

Set의 합집합 구현

예제 코드

```
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}

// 사용 예시
Set<Integer> integers = Set.of(1, 3, 5);
Set<Double> doubles = Set.of(2.0, 4.0);
Set<Number> numbers = union(integers, doubles); // 컴파일 가능
```

설명

두 개의 Set을 합치면서 E 타입을 상속한 모든 타입을 지원한다.

유연하면서도 타입 안전성을 유지한다.

Target Typing과 자바 버전별 차이

자바 7 이하

```
Set<Number> numbers = Union.<Number>union(integers, doubles);
```

-   명시적으로 타입 파라미터를 지정해야 한다.

자바 8 이상

```
Set<Number> numbers = union(integers, doubles);
```

-   Target Typing이 지원되어 왼쪽 변수의 타입을 기반으로 추론이 가능하다.