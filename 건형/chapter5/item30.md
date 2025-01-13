## 📖 이왕이면 제네릭 메서드로 만들라

### 핵심 내용

- 제네릭 타입과 마찬가지로, 클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변횐해야 하는 메서드보다 제네릭 메서드가 더 안전하며 사용하기도 쉽다.

- 다입과 마찬가지로, 메서드도 형변환 없이 사용할 수 있는 편이 좋으먀, 많은 경우 그렇게 하려면 제네릭 메서드가 되어야 한다.

- 역시 타입과 마찬가지로, 형변환을 해줘야 하는 기존 메서드는 제네릭하게 만들자.

- 기존 클라이언트는 그래도 둔 채 새로운 사용자의 삻을 훨씬 편하게 만들어줄 것이다.


## 💡 주요 내용 정리 & 🛠️ 실습 코드

- 클래스와 마찬가지로, 메서드도 제네릭으로 만들 수 있다.

- 매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다.

제네릭 메서드
```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

제네릭 메서드를 활용하는 간단한 프로그램
```java
public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
    System.out.println(aflCio);
}
```


제네릭 싱클턴 팩터리 패턴
```java
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SupperssWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

제네릭 싱글턴을 사용하는 예
```java
public static void main(String[] args) {
    String[] strings = {"삼베", "대마", "나일론"};
    
    UnaryOperator<String> sameString = identityFunction();
    for (String s : strings) {
        System.out.println(sameString.apply(s));
    }

    Number[] numbers = {1, 2.0, 3L};

    UnaryOperator<Number> sameNumber = identityFunction();
    for (Number s : numbers) {
        System.out.println(sameNumber.apply(n));
    }
}
```

## 추가 강의 내용 정리

### Type safe한 Generic method

```java
List<String> stringList = List.of("T1","T2","T3");

// ...
static <E> List<E> of(E e1, E e2, E e3) {
    return new ImmutableCollections.ListN<>(e1, e2, e3);
}
// ...

// object는 제네릭으로 캐스팅 되지 않음, 유형이 다르기 때문임 에러 발생
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

public static <T> UnaryOperator<T> identityFunction() {
    return IDENTITY_FN;
}

// 해결책

public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}

```

### Type 한정

Method의 parameter 타입을, interface의 Type으로 한정한다.
```java
interface Comparable<T> {
    int compare(T o);
}
```

### 정리

generic 타입과 같이 형변환 해야하는 method보다

generic method가 더 안전하고, 심지어 사용하기도 쉽다.

형변환 해야 하는 메서드는 generic하게 만들자!


## 🤔 생각 정리

- 다양한 예시들이 몇 개 더 있었지만, 아직 구체적으로 이해가 가지 않는다...
- 이번 장은 정리에서 이야기하는 핵심 요약인 '형변환 해야 하는 메서드는 Generic 하기 만들자!'라는 내용만 숙지하고 넘어가보자

