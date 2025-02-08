## 아이템 58 - 전통적인 for 문보다는 for-each를 사용하라

### 전통적인 for 문과 for-each의 차이

전통적인 for 문은 반복자(iterator)나 인덱스를 직접 다루어야 하기 때문에 코드가 복잡해지고 실수할 가능성이 높다.

인덱스를 잘못 설정하거나 반복 조건을 누락하는 등의 오류가 발생하기 쉽다.

반면, for-each 문은 컬렉션 또는 배열의 원소를 순차적으로 처리하는 데 최적화되어 있다.

코드가 간결하고 가독성이 높아지며, 반복 조건을 신경 쓰지 않아도 된다.

전통적인 for 문 예시

```java
List<String> stringList = new ArrayList<>();
for (int i = 0; i < stringList.size(); i++) {
    String s = stringList.get(i);
    // 처리 로직
}
```

for-each 문 예시

```java
for (String s : stringList) {
    // 처리 로직
}
```

-   for-each 문의 장점  
    가독성이 향상되고 코드가 간결해진다.  
    반복자나 인덱스를 직접 다루지 않으므로 오류 발생 가능성이 줄어든다.  
    대부분의 Iterable 객체에서 사용 가능하다.

### for-each 문의 한계와 예외 상황

for-each 문은 원소를 읽고 처리하는 데 적합하지만, 원소를 변경해야 하는 경우에는 사용이 어렵다.

특히 순회 중에 원소를 제거하려고 하면 ConcurrentModificationException이 발생할 수 있다.

이런 상황에서는 Iterator를 사용하여 안전하게 원소를 제거하는 것이 필요하다.

Iterator는 컬렉션의 상태 변화를 추적하며, remove() 메서드를 통해 안전하게 제거를 수행할 수 있다.

-   원소 제거 예시 (iterator 사용)

```java
Iterator<String> iterator = stringList.iterator();
while (iterator.hasNext()) {
    String s = iterator.next();
    if (s.equals("test")) {
        iterator.remove();
    }
}
```

-   Java 8부터는 removeIf() 메서드를 사용할 수도 있다.

```java
stringList.removeIf(s -> s.equals("test"));
```