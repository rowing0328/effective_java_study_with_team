## 아이템 45 - 스트림은 주의해서 사용하라

### 스트림을 사용하지 말아야 하거나 사용할 수 없는 상황

-   컨트롤 흐름이 중요한 경우  
    예 : return, break, continue와 같은 제어 흐름을 사용해야 할 때 스트림은 적합하지 않다.  
    스트림 내부에서는 이러한 제어 흐름을 구현하기 어렵고, 코드의 가독성이 떨어질 수 있다.
-   순수 함수형 패러다임을 벗어나는 경우  
    스트림 내에서 지역 변수를 변경하거나, 외부 상태를 조작해야 한다면 스트림은 피해야 한다.

```
int a = 1;
List.of("사과", "배").stream().filter(str -> {
    if (str.equals("배")) {
        a = 2; // 외부 변수 수정 - 금지
    }
    return true;
});
```

### 스트림을 사용해야 할 때

원소의 변환

-   예 : List<ItemInfo>를 List<String>으로 변환할 때

```
list.stream().map(ItemInfo::getName).collect(Collectors.toList());
```

필터링

-   특정 조건에 맞는 데이터를 필터링 할 때

```
list.stream().filter(item -> item.isActive()).collect(Collectors.toList());
```

집계 연산

-   데이터의 합계, 최소값, 최대값 등을 구할 때

```
int sum = list.stream().mapToInt(Item::getPrice).sum();
```

컬렉션 변환

-   데이터를 다른 컬렉션으로 반환할 때

```
list.stream().collect(Collectors.toSet());
```

특정 조건 만족 여부 검사

-   데이터 중 특정 조건을 만족하는 첫 번째 요소 찾기

```
list.stream().findFirst();
```

### 스트림 방식과 반복 방식 비교

반복 방식

반복문을 사용한 데이터 처리 방식은 명시적이고 직관적이지만, 코드가 장황해질 수 있다.

```
public static List<Integer> minFive(List<Integer> integerList) {
    List<Integer> returnList = new ArrayList<>();
    List<Integer> copiedList = new ArrayList<>(integerList);
    Collections.sort(copiedList);
    
    for (int i = 0; i < copiedList.size(); i++) {
        if (i == 5) break;
        returnList.add(copiedList.get(i));
    }
    
    return returnList;
}
```

스트림 방식

스트림을 활용하면 코드를 간결하게 작성할 수 있다.

```
public static List<Integer> minFiveExtend(List<Integer> integerList) {
    return integerList.stream()
                      .sorted()
                      .limit(5)
                      .collect(Collectors.toList());
}
```
