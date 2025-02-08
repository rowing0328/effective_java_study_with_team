## 아이템 47 - 반환 타입으로는 스트림과 컬렉션이 낫다.

과거 Iterator를 사용했던 시절

과거에는 데이터를 반복해서 처리할 때 Iterator를 활용하는 방식이 일반적이었다.

Iterator는 데이터를 순회하기에 적합한 도구이지만,

Stream의 등장으로 코드의 가독성과 유지보수성이 크게 향상되었다.

예를 들어, 아래와 같은 Iterator 사용 방식은 반복적인 작업에서는 적합하지만, 코드가 장황해질 수 있다.

```
List<Integer> list = List.of(1, 2, 3);
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    System.out.println("value = " + iterator.next());
}
```

### Stream과 Iterable의 관계

Stream은 Iterable을 확장하지 않기 때문에,

기본적으로 for-each 문으로 반복할 수 없다.

이를 해결하기 위해 Stream과 Iterable 사이의 어댑터를 정의할 수 있다.

Stream -> Iterable 변환

-   Stream을 Iterable로 변환하면 for-each 문에서 사용할 수 있다.

```java
public static <E> Iterable<E> iterableOf(Stream<E> stream) {
    return stream::iterator;
}
```

Iterable -> Stream 변환

-   반대로, Iterable을 Stream으로 변환하면 Stream의 장점을 활용할 수 있다.

```java
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
    return StreamSupport.stream(iterable.spliterator(), false);
}
```

### 반환 타입으로 스트림과 컬렉션 중 무엇을 선택하는가

-   Stream  
    Stream Pipeline에서 데이터를 처리할 때 적합합니다.  
    복잡한 연산이 많을 경우 Stream을 반환하는 것이 유용합니다.
-   Iterable  
    단순히 반복문에서 사용할 경우 Iterable 반환이 적합합니다.

권장 사항

-   컬렉션으로 반환할 수 있다면, 컬렉션을 반환하라.
-   불가능한 경우에만 Stream 또는 Iterable 중 적합한 타입을 반환하라.
