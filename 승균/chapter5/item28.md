## item28: 배열보다는 리스트를 사용하라

## **핵심 주제**

**배열과 제네릭 타입에는 중요한 차이가 두 가지 있다.**
1. 배열은 공변(covariant)이다. => Sub가 Super 하위 타입이라면 배열 Sub[]는 배열 Super[]의 하위 타입이 된다.
2. 제네릭은 불공변(invariant)이다. => 즉, 서로 다른 타입 Type1과 Type2가 있을 때, List<Type1>은 List<Type2>의 하위 타입도 아니고 상위 타입도 아니다.

**런타임에 실패한다.**
```java
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."; // ArrayStoreException을 던진다. 
```

**컴파일되지 않는다.**

```java
import java.util.ArrayList;

List<Object> ol = new ArrayList<Long>(); 호환되지 않는 타입이다.
ol.add("타입이 달라 넣을 수 없다.");
```
어느 쪽이든 Long용 저장소에 String 넣을 수는 없다. 

다만 배열에서는 그 실수를 런타임에야 알게 되지만, 리스트를 사용하면 컴파일할 때 바로 알 수 있다.

두 번째 주요 차이로, 배열은 실체화(reify)가 된다. 배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다. <br/>
Long 배열에 String을 넣으려 하면 ArrayStoreException 발생한다. <br/>
원소 타입을 컴파일타임에만 검사하며 런타임에는 알수조차 없다는 의미이다.

배열과 제네릭은 잘 어우러지지 못한다. <br/>
배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다.<br/>
제네릭 배열을 만들지 못하게 막은 이유는? 타입이 안전하지 않기 때문이다. <br/>
이를 허용한다면 컴파일러가 자동 생성한 형변환 코드에서 런타임에 ClassCastException이 발생할 수 있다.

**제네릭 배열 생성을 허용하지 않는 이유 - 컴파일되지 않는다.**
```java
List<String>[] stringLists = new List<String>[1]; (1)
List<Integer> intList = List.of(42); (2)
Object[] objects = stringLists; (3)
objects[0] = intList; (4)
String s = stringLists[0].get(0); (5)
```

제네릭 배열을 생성하는 (1)이 허용된다고 가정해보자. <br/>
(2) 원소가 하나인 List<Integer>를 생성한다. <br/>
(3)은 (1)에서 생성한 List<String>의 배열을 Object 배열에 할당한다. => 배열은 공변이니 문제없음 <br/>
(4)는 (2)에서 생성한 List<Integer>의 인스턴스를 Object 배열의 첫 원소로 저장한다. 제네릭은 소거 방식으로 되어서 이 역시 성공한다. <br/>
런타임에는 List<Integer> 인스턴스의 타입이 단순히 list가 되고, List<Integer>[] 인스턴스의 타입은 List[]가 된다.

ArrayStoreException에러를 일으키지 않는다.

이제부터가 문제다. List<String> 인스턴스만 담게다고 선언한 stringLists 배열에는 지금 List<Integer> 인스턴스가 저장돼 있다. <br/>
그리고 (5)는 이 배열의 처음 리스트에서 첫 원소를 꺼내려함 <br/>
컴파일러는 꺼낸 원소를 자동으로 String으로 형변환하는데, 이 원소는 Integer이므로 런타임에 ClassCastException 이 발생한다. <br/>

E, List<E>, List<String> 같은 타입을 실체화 불가 타입(non-reifiable type)이라 한다. 

실체화 되지 않아서 런타임에는 컴파일타임보다 타입정보를 적게 가지는 타입이다.

소거 메커니즘 때문에 매개변수화 타입 가운데 실체화 될 수 있는 타입은 List<?>와 Map<?,?> 같은 비한정적 와일드 카드뿐이다.

**Chooser - 제네릭을 시급히 적용해야 한다!**
```java

public class Chooser {
    private  final T[] choiceArray;
    
    public Chooser(Collections<T> choices) {
        choiceArray = choices.toArray();
    }
}

```
**Chooser를 제네릭으로 만들기 위한 첫 시도 - 컴파일되지 않는다.**
```
public class Chooser<T> {
    private final T[] choiceArray;
    
    public Chooser(Collection<T> choices) {
        choiceArray = choices.toArray();
    }
}
```

Object 배열을 T 배열로 형변환하면 된다.
```java
choiceArray = (T[]) choices.toArray();
```

동작은 하지만, 컴파일러가 안정성을 보장을 하지 못할 뿐임.

비검사 형변환 경고를 제거하려면 배열 대신 리스트를 쓰면 된다.
**리스트 기반 Chooser- 타입 안정성 확보!**

```java
import java.util.ArrayList;
import java.util.Random;

public class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collections<T> choices) {
        choiceList = new ArrayList<>(choices);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```