## 아이템 46 - 스트림에서는 부작용 없는 함수를 사용하라

### Stream은 순수 함수여야 한다

Java Stream API는 순수 함수형 프로그래밍의 개념을 차용했다.

순수 함수란 오직 입력값에만 의존하며,

결과값을 반환할 뿐 다른 외부 상태에 영향을 주지 않는 함수를 말한다.

Stream을 사용할 때도 이러한 순수성을 유지해야 코드의 예측 가능성과 유지보수성이 높아진다.

순수 함수의 특징  
오직 입력값만 결과값에 영향을 미친다.

다른 상태를 참조하거나 변경하지 않는다.

다음은 순수하지 않은 방식으로 Stream을 사용한 사례

-   아래 코드는 외부 리스트 returnList를 Stream 내부에서 조작하고 있어 부작용이 발생한다.

```java
public static List<Integer> integerSort(List<Integer> integerList) {
    List<Integer> returnList = new ArrayList<>();
    
    integerList.stream().sorted().forEach(num -> {
        returnList.add(num); // 외부 리스트 상태 변경
    });
    
    return returnList;
}
```

올바른 코드의 예시

-   아래 코드는 Stream의 결과를 바로 반환하며, 외부 상태를 변경하지 않아 순수성을 유지한다.

```java
public static List<Integer> integerSort(List<Integer> integerList) {
    return integerList.stream()
                      .sorted()
                      .collect(Collectors.toList()); // Collectors 사용
}
```

### Stream에서 자주 사용되는 Collector 메서드

Stream API의 다양한 Collector 메서드를 활용하면 데이터를 효율적으로 처리할 수 있다.

-   toMap  
    데이터를 키-값 쌍으로 매핑할 때 사용한다.

```java
public static Map<Long, String> getHeightGroup(List<User> userList) {
    return userList.stream()
                   .collect(Collectors.toMap(User::getId, User::getName));
}
```

-   groupingBy  
    데이터를 특정 기준으로 그룹화할 때 유용하다.

```java
public static Map<Integer, Long> getHeightGroup(List<User> userList) {
    return userList.stream()
                   .collect(Collectors.groupingBy(
                       user -> (int) user.getHeight() / 5 * 5, 
                       Collectors.counting()
                   ));
}
```
