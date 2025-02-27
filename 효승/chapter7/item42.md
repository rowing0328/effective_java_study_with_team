## 아이템 42 - 익명 클래스보다는 람다를 사용하라

### 자바 8, 그리고 람다

자바 8에서는 함수형 프로그래밍의 도입으로 인해,

작은 함수 객체를 구현하기에 람다(lambda)가 도입되었다.

람다는 함수형 인터페이스를 지향하며,

코드의 간결함과 가독성을 높이는데 초점을 맞춘다.

특히, 타입을 명시해야 코드가 더 명확해지는 상황을 제외하고는,

람다의 매개변수 타입은 생략하는 것이 좋다.

이는 컴파일러가 타입을 추론할 수 있는 환경을 만들어주기 때문이다.

### 익명 함수와 람다의 비교

기존의 익명 클래스와 람다를 비교하여 람다의 장점을 살펴보자.

예제 코드

```java
// 익명 클래스
List<String> words = List.of("사과", "배", "바나나");

Collections.sort(words, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return Integer.compare(o1.length(), o2.length());
    }
});

// 람다 사용
words.sort(Comparator.comparingInt(String::length));
```

설명

람다를 사용하면 코드의 간결함과 가독성이 크게 향상된다.

특히, 위 예제처럼 Comparator와 같은 함수형 인터페이스를 사용할 때는 람다의 효과가 극대화된다.

### 람다 사용 시 주의점

람다는 간결한 만큼 복잡한 로직을 담아서는 안된다.

람다 내부에 여러 줄의 코드가 포함되면 오히려 가독성이 떨어질 수 있으므로,

명확하고 간단한 작업에만 사용해야 한다.

### 언제 익명 클래스를 사용할까?

-   함수형 인터페이스가 아닌 경우  
    람다는 함수형 인터페이스(단일 추상 메서드)를 구현할 때만 사용할 수 있다.
-   람다로 표현하기 어려운 상태를 가진 구현이 필요한 경우  
    예를 들어, 특정 상태를 유지해야 하는 로직이 포함된 경우 익명 클래스가 더 적합하다.
