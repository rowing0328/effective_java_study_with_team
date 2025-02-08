## 아이템 55 - Optional 반환은 신중히 하라

Optional<T>는 값이 존재할 수도 있고 존재하지 않을 수도 있는 컨테이너 객체이다.

주로 메서드가 값이 없을 수 있는 상황에서 null 대신 안전하게 반환값을 처리하기 위해 사용된다.

예제 코드 - Optional 사용

```
Optional<Laptop> optionalLaptop = laptopRepository.findById(id);
Laptop laptop = optionalLaptop.orElse(null);  // 권장되지 않음
```

API 노트에 따르면, Optional은 메서드의 반환값으로 사용하도록 설계되었다.

-   변수 자체가 Optional 타입일 때 null로 설정하지 말고 항상 Optional 객체를 반환해야 한다.
-   null 반환이 예상되는 경우 Optional로 감싸서 명시적으로 처리한다.

### 올바른 Optional 사용법

Optional은 값이 없을 경우 기본값을 제공하거나, 예외를 던질 때 유용하다.

예시 코드 - 기본값 사용

```
public int maxPositiveIntegerValue(List<Integer> integerList) {
    return getMaxInteger(integerList).orElse(0);  // 기본값 0 반환
}

private OptionalInt getMaxInteger(List<Integer> integerList) {
    return integerList.stream()
                      .mapToInt(Integer::intValue)
                      .filter(integer -> integer > 0)
                      .max();
}
```

예제 코드 - 예외 처리

```
public T get() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
}
```

### Optional을 사용하지 말아야 할 경우

Optional이 항상 적합한 것은 아니다. 다음과 같은 경우에는 Optional 사용이 권장되지 않는다.

-   컨테이너 타입 (예: 컬렉션, 배열)  
    빈 컬렉션 또는 배열을 반환하는 것이 더 적절한다.

```
public List<String> getNameList() {
    return Collections.emptyList();
}
```

-   기본 타입 (Primitive Type)  
    OptionalInt, OptionalDouble과 같은 전용 클래스를 사용해야 한다.

-   잘못된 예시

```
public ItemInfo getItemInfo() {
    return itemInfoRepository.findById(1L).orElse(null);  // Optional을 무시하고 null 반환
}
```
