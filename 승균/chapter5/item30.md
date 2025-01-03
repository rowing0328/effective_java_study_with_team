## item30: 이왕이면 제네릭 타입으로 만들라

## **핵심 주제**

클래스와 마찬가지로, 메서드도 제니릭으로 만들 수 있다.

매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다.

예컨대 Collections의 '알고리즘'메서드(binarySearch, sort 등) 모두 제네릭이다.

제네릭 메서드 작성방법은 제네릭 타입 작성법과 비슷

**로 타입 사용- 수용불가!**
```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

컴파일은 되지만 경고가 두 개 발생

```
Union.java5: warning: [unchecked] call to
HashSet(Collection<? extends E>) as a member of raw type HashSet
    Set result = new HashSet(s1);
    
Union.java6: warning: [unchecked] unchecked call to
addAll(Collection<? extends E>) as a member of raw type Set 
    result.addAll(s2);
```

경고를 없애려면 이 메서드를 타입 안전하게 만들어야 한다. 메서드 선언에서의 세 집합(입력 2개, 반환 1개)의 원소타입 매개변수로 명시하고, 메서드 안에서도 이 타입 매개변수만 사용하게 수정하면 된다.

**(타입 매개변수들을 선언하는) 타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 온다.**

타입 매개 변수의 명명 규칙은 제네릭 메서드나 제네릭 타입이나 똑같다.

**제네릭 메서드**
```
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

단순한 제네릭 메서드라면 이 정도면 충분하다. 

이 메서드는 경고 없이 컴파일되며, 타입 안전하고, 쓰기도 쉽다.

**제네릭 메서드를 활용하는 간단한 프로그램**
```java
public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guyts, stooges);
    System.out.println(aflCio);
}
```

이 프로그램을 실행하면 "[모에, 톰, 해리, 래리, 컬리, 딕]"이 출력된다.

때때로 불변 객체를 여러 타입으로 활용할 수 있게 만들어야 할 때가 있다. <br/>
제네릭은 런타임에 타입 정보가 소거 되므로 하나의 객체를 어떤 타입으로든 매개변수화 할 수 있다. <br/>
하지만 이렇게 하려면 요청한 타입 매개 변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 한다. <br/>
이 패턴을 제네릭 싱글턴 팩터리라 하며, Collections.reverseOrder 같은 함수 객체나 Collections.emptySet 같은 컬렉션용으로 사용한다. <br/>

**제네릭 싱글턴 팩터리 패턴**

```java
import java.util.function.UnaryOperator;

private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

IDENTITY_FN을 UnaryOperator<T>로 형변환하면 비검사 형변환 경고가 발생한다.

T가 어떤 타입이든 UnaryOperator<Object>는 UnaryOperator<T>가 아님

항등함수는 입력 값을 수정 없이 그대로 반환하기 때문에, T가 어떤 타입이든 UnaryOperator<Object>를 사용해도 안전하다.


**제네릭 싱글턴을 사용하는 예**

```java
import java.util.function.UnaryOperator;

public static void main(String[] args) {
    String[] strings = {"삼베", "대마", "나일론"};
    UnaryOperator<String> sameString = identityFunction();
    
    for (String s: strings) {
        System.out.println(sameString.apply(s));
    }
    
    Number[] numbers = { 1, 2.0, 3L };
    UnaryOperator<Number> sameNumber = identityFunction();
    for (Number n : numbrers)
        System.out.println(sameNumber.apply(n));
}
```

자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있다. 

바로 재귀적 타입 한정(recursive type bound)라는 개념이다.

재귀적 타입 한정은 주로 타입의 자연적 순서를 정하는 Comparable 인터페이스와 함께 쓰인다.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

여기서 타입 매개변수 T는 Comparable<T>를 구현한 타입이 비교할 수 있는 원소의 타입을 정의 << 이 기능을 수행하려면 컬렉션에 담긴 모든 원소가 상호 비교 될 수 있어야 한다.

**재귀적 타입을 한정을 이용해 상호 비교할 수 있음을 표현했다.**
```java
public static <E extends Comparable<E>> E max(Collection<E> c);
```

타입 한정인 <E extends Comparable<E>>는 '모든 타입 E는 자신과 비교할 수 있다.'라고 읽을 수 있다. 

상호 비교 가능하다는 뜻을 아주 정확하게 표현했다고 할 수 있음.

**컬렉션에서 최대값을 반환한다. - 재귀적 타입 한정 사용**
```java
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty()) throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    
    E result = null;
    for (E e : c)
        if( result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);
    
    return result;
}
```