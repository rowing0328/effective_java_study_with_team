## 📖 배열보다는 리스트를 사용하자

### 핵심 내용

- 배열과 제네릭에는 매우 다른 타입 규칙이 적용된다.

- 배열은 공변이고 실체화되는 반면, 제네릭은 불공변이고 타입 정보가 소거된다.

- 그 결과 배열은 런타임에는 타입 안전하지만 컨파일타임에는 그렇지 않다.

- 제네릭은 반대다. 그래서 둘을 섞어 쓰기란 쉽지 않다.

- 둘을 섞어 쓰다가 컴파일 오류나 경고를 만나면, 가장 먼저 배열을 리스트로 대체하는 방법을 적용해보자.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

### 배열과 제네릭 타입의 중요한 차이

- 배열은 공변이다.
    - Sub가 Super의 하위 타입이라면 배열 Sub[]는 배열 Super[]의 하위 타입이 된다.
  
- 제네릭은 불공변이다.
   - 서로 다른 타입 Type1과 Type2가 있을 때, List<Type1>은 List<Type2>의 하위 타입도 아니고 상위 타입도 아니다.

런타임에 실패한다.
```java
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."; // ArrayStoreException을 던진다.
```

하지만 다음 코드는 문법에 맞지 않는다.
```java
List<Object> ol = new ArrayList<Long>(); // 호환되지 않는 타입이다.
ol.add("타입이 달라 넣을 수 없다.");
```

- 어느 쪽이든 Long용 저장소에 String을 넣을 수는 없다.
- 다만 배열에서는 그 실수를 런타임에야 알게 되지만, 리스트를 사용하면 컴파일할 때 바로 알 수 있다.

- 배열은 실체화된다. (reify)
  - 배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다.
  - 반면에, 제네릭은 타입 정보가 런타임에는 소거(erasure)된다.
  - 원소 타입을 컴파일타임에만 검사하며 런타임에는 알수조차 없다.

- 배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다.
  - 컴파일할 때 제네릭 배열 생성 오류를 일으킨다.

- 제네릭 배열을 만들지 못하게 막은 이유
  - 타입 안전하지 않기 때문
  - 허용한다면 컴파일러가 자동 생성한 형변환 코드에서 런타입 ClassCastException이 발생할 수 있다.

제네릭 배열 생성을 허용하지 않는 이유 - 컴파일되지 않는다.
```java
List<String>[] stringLists = new List<String>[1]; // (1)
List<Integer> intList = List.of(42); // (2)
Object[] objects = stringLists; // (3)
object[0] = intList; // (4)
String s = stringLists[0],get(0); // (5)
```

### E, List<E>, List<String> 같은 타입을 실체화 불가 타입(non-reifiable type)이라 한다.

- 실체화 되지 않아서 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입이다.
- 소거 메커니즘 때문에 매개변수화 타입 가운데 실체화될 수 있는 타입은 비한정적 와일드카드 타입뿐이다.

### 배열로 형변환달 때 제네릭 배열 생성 오류나 비검사 형변환 경고가 뜨는 경우 대부분은 배열인 E[] 대신 컬렉션 List<E>를 사용하면 해결된다.

- 코드가 조금 복잡해지고 성능이 살짝 나빠질 수도 있지만, 그 대신 타입 안정성과 상호운용성은 좋아진다.

Chooser - 제네릭을 시급히 적용해야 한다!
```java
public class Chooser{ 
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

위 클래스를 사용하려면 choose 메서드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야 한다.

리스트 기반 Chooser - 타입 안전성 확보!
```java
public class Chooser<T> {
    private final List<T> choiceList;
    
    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }
    
    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

**비검사 형변환 경고를 제거하려면 배열 대신 리스트를 사용하자!!**


## 추가 강의 내용 정리

### 배열 보다는 제네릭

Runtime Error
```java
Object[] objects = new Long[1];
object[0] = "test";
```

Compile Error
```java
List<Object> objectList = new ArrayList<Long>(0);
```

### 정리

- 코딩 테스트가 불러온 미신 -> Array가 List보다 빠르기 때문에 가능한 Array로 문제를 푸는 것이 좋다
- 따라서 배열을 쓰는 것이 속도에 가장 좋다 (X)

- 차라리 LinkedList와 ArrayList의 차이점을 잘 알고 쓰는 것이 더 중요하다.

- 우리가 만드는 Product level에서는 ArrayList만으로도 충분한 경우가 대부분이다.


## 🤔 생각 정리

- 배열을 쓰는 경우는 거의 없긴 했지만, 배열을 사용하지 않는 근거를 명확히 이야기 할 수 있는 챕터였다.

