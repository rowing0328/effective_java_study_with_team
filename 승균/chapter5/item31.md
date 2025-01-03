## item31: 한정적 와일드카드를 사용해 API 유연성을 높이라

## **핵심 주제**
매개변수화 타입은 불공변(invariant)이다.

서로 다른 타입 Type1과 Type2가 있을 때 List<Type1>은 List<Type2>의 하위 타입도 상위 타입도 아니다. <br/>
List<String>은 List<Object>의 하위 타입이 아니라는 뜻

List<Object>에는 어떤 객체든 넣을 수 있지만, List<String>에는 문자열만 넣을 수 있다. <br/>
즉, List<String>은 List<Object>가 하는 일을 제대로 수행하지 못하니 하위 타입이 될 수 없다.(리스코프 치환 원칙에 어긋남)

하지만 때론 불공변 방식보다 유연한 무언가가 필요하다. 

```
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
```

**와일드카드 타입을 사용하지 않은 pushAll 메서드 - 결함이 있다!**
```java
public void pushAll(Iterable<E> src) {
    for (E e : src) 
        push(e);
}
```

깨끗이 컴파일되지만 완벽하진 않다. <br/>
Iterable src의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다. <br/>
Stack<Number>로 선언한 후 pushAll(intVal)을 호출하면 어떻게 될까? <br/>
여기서 intVal은 Integer타입이다. <br/>
Integer는 Number의 하위 타입이니 잘 동작한다.

```java
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> integers = ...;
numberStack.pushAll(integers);
```

하지만 실제로는 다음의 오류 메시지가 뜬다. 매개변수화 타입이 불공변이기 떄문이다. <br/>

```
StackTest.java7: error: incompatible types: Iterable<Integer>
cannot be converted to Iterable<Number>
        numberStack.pushAll(integers);
```

자바는 이런 상황에 대처할 수 있는 한정적 와일드카드타입이라는 특별한 매개변수화 타입을 지원한다.

pushAll의 입력 매개변수 타입은 'E의 iterable'이 아니라 'E의 하위 타입의 Iterable'이어야 하며, 와일드 카드 타입 Iterable<? extends E> 가 정확히 이런 의미다.

**생산자(producer)매개변수에 와일드카드 타입 적용**
```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) 
        push(e);
}
```

이번 수정으로 Stack은 물론 이를 사용하는 클라리언트 코드도 말끔히 컴파일 된다.

이제 pushAll과 짝을 이루는 popAll메서드를 작성할 차례다. popAll 메서드는 Stack안의 모든 원소를 주어진 컬렉션으로 옮겨 담는다.

**와일드카드 타입을 사용하지 않은 popAll메서드 - 결함이 있다.**
```java
public void popAll(Collection<E> dst) {
    while (!isEmpty())
        dst.add(pop());
}
```

이번에도 주어진 컬렉션의 원소 타입이 스택의 원소 타입과 일치한다면 말끔히 컴파일되고 문제없이 동작한다.

Set<Number>의 원소를 Object용 컬렉션으로 옮긴다고 생각해보자.

```java
Stack<Number> numberStack = new Stack<>();
Collections<Object> objects = ...;
numberStack.add(objects);
```

이 클라이언트 코드를 앞의 popAll 코드와 함께 컴파일하면 "Collection <Object>는 Collection<Number>의 하위 타입이 아니다"라는, 코드 31-1 pushAll을 사용했을 때와 비슷한 오류가 발생함.

popAll의 입력 매개변수의 타입이 'E의 Collection' 이 아니라 'E의 상위 타입의 Collection' 이어야 한다.

**소비자(consumer) 매개변수에 와일드카드 타입 적용**

```java
import java.util.Collection;

public void popAll(Collection<? super E> dst) {
    while(!isEmpty()) {
        dst.add(pop());
    }
}
```

**유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드 카드 타입을 사용하라**

한편, 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을게 없다. <br/>
타입을 정확히 지정해야 하는 상황으로, 이 때는 와일드카드 타입을 쓰지 말아야 한다 .

펙스(PECS): producer-extends, consumer-super

즉, 매개변수화 타입 T가 생산자라면 <? extends T>를 사용하고, 소비자라면 <? super T>를 사용하라. <br/>
Stack 예에서 pushAll의 src 매개변수는 Stack이 사용할 E 인스턴스를 생산하므로 src의 적절한 타입은 Iterable<? extends E> 이다. <br/>
한편, popAll의 dst 매개변수는 Stack으로부터 E 인스턴스를 소비하므로 dst의 적절한 타입은 Collection<? super E>이다. 

PECS 공식은 와일드카드 타입을 사용하는 기본 원칙이다. <br/>
나프탈린과 와들러는 이를 겟풋원칙(Get and Put Principle)으로 부른다. 

```java
public Chooser(Collection<T> choices)
```

이 생성자로 넘겨지는 choices 컬렉션은 T 타입의 값을 생산하기만 하니, T를 확장하는 와일드카드 타입을 사용해 선언해야 한다.

```java
public Chooser(Collection<? extends T> choices)
```

Chooser<Number>의 생성자에 List<Integer> 넘기고 싶다고 해보자.

수정 전 생성자로는 컴파일조차 되지 않겠지만, 한정적 와일드 카드 타입으로 선언한 수정 후 생성자에서는 문제가 사라진다.

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2)
```

s1, s2 모두 생산자이니, PECS 공식처럼 
```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
```

반환 타입은 여전히 Set<E>임에 주목 </br>
반환 타입에는 한정적 와일드카드 타입을 사용하면 안 된다. <br/> 
유연성을 높여주기는커녕 클라이언트 코드에서도 와일드 카드 타입을 써야 하기 때문이다. <br/>

```java
Set<Integer> integers = Set.of(1, 3, 5);
Set<Double> doubles = Set.of(2.0, 4.0, 6.0);
Set<Number> numbers = union(integers, doubles);
```

제대로 사용한다면 클래스 사용자는 와일드 카드 타입이 쓰였다는 사실 조차 인식 할 수 없다. <br/>
받아들여야 할 매개변수를 받고 거절해야 할 매개변수는 거절하는 작업이 알아서 이뤄진다.

**클래스 사용자가 와일드카드 타입을 신경 써야 한다면 API에 문제가 있을 가능성이 크다.**

컴파일러가 올바른 타입을 추론하지 못하면 명시적 타입 인수를 사용해서 타입을 알려주면 된다.

목표 타이핑(target typing) 자바8부터 지원하기 시작했는데, 그 전 버전에는 이런 문제가 흔하진 않았다. <br/>
자바 7이하에서도 깨끗이 컴파일 된다.

**자바 7까지는 명시적 타입 인수를 사용해야 한다.**
```java
Set<Number> numbers = Union.<Number>union(integers, doubles);
```

(옮긴이) 매개변수(parameter)와 인수(argument)의 차이를 알아보자. 

매개변수는 메서드 선언에 정의한 변수이고, 인수는 메소드 호출 시 넘기는 '실젯값'이다.

```java
void add(int value) { ... }
add(10)
```

이 코드에서 value는 매개변수 10은 인수다. 이 정의를 제네릭까지 확장하면 다음과 같다.
```
class Set<T> { ... }
Set<Integer> = ...;
```

여기서 T는 타입 매개변수가 되고, Integer는 타입 인수가 된다.

```java
public static <E extends Comparable<E>> E max (List<E> list)
```

 
```java
public static <E extends Comparable<? super E>> E max(List<? extends E> list);
```

입력 매개변수에서는 E 인스턴스를 생성하므로 List<E> -> List<? extends E>로 수정

다음은 더 난해한 쪽인 타입 매개변수 E로, 이 책에서 타입 매개변수에 와일드카드를 적용한 첫 번째 예이기도 하다.

원래 선언에서는 E Comparable <E> 확장한다고 정의, 이 때, Comparable<E>는 E 인스턴스를 소비하는 것이기에 Comparable<? super E>로 대체

Comparable 언제나 소비자이므로, 일반적으로 **Comparable<E>보다는 Comparable<? super E>를 사용하는 편이 낫다.**

Comparator도 마찬가지다. 일반적으로 Comparator<E>보다는 Comparator<? super E>를 사용하는 편이 낫다.

List<ScheduledFuture<?>> scheduledFutures = ...;

수정 전 max가 이 리스트를 처리 할 수 없는 이유 (java.util.concurrent 패키지의) ScheduledFuture는 Delayed의 하위 인터페이스이고, Delayed의 하위 인터페이스이고, Delayed는 Comparable<Delayed>를 확장했다. 

다시 말해, ScheduledFuture 인스턴스는 다른 Scheduled 인스턴스뿐 아니라 Delayed 인스턴스와도 비교할 수 있어서 수정 전 max가 이 리스트를 거부하는 것이다. <br/>
더 일반화해서 말하면, Comparable(혹은 Comparator)을 직접 구현하지 않고, 직접 구현한 다른 타입을 확장한 타입을 지원하기 위해 와일드카드가 필요하다.

```java
public interface Comparable<E>
public interface Delayed extends Comparable<Delayed>
public interface ScheduledFuture<V> extends Delayed, Future<V>
```

타입매개변수와 와일드카드에는 공통되는 부분이 있어서, 둘중 어느 것을 사용해도 괜찮을 때가 있음.
주어진 리스트에서 명시한 두 인덱스의 아이템들을 교환(swap)하는 정적 메서드를 두 방식 모두로 정의해보자.

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

public API라면 간단한 두 번째가 더 낫다. 어떤 리스트든 이 메서드에 넘기면 명시한 인덱스의 원소들을 교환해 줄 것이다.

**메서드 선언에 타입 매개변수가 한 번만 나오면 와일드 카드로 대체하라.**

비한정적 타입 매개변수라면 비한정적 와일드카드로 바꾸고, 한정적 타입 매개변수라면 한정적 와일드 카드로 바꾸면 된다.

```java
public static void swap(List<?> list, int i, int j) {
    list.set(i, list.set(i)));
}
```

컴파일하면 에러가 발생한다.

```
Swap.java5: error: incompatible types: Object cannot be converted to CAP#1
    list.set(i, list.set(j, list.get(i)));
    
   where CAP#1 is a fresh type-variable:
    CAP#1 extends Object from capture of ?
```

리스트의 타입이 List<?>인데, List<?>에는 null 외에는 어떤 값도 넣을 수 없다.
다행히 형변환이나 리스트의 로 타입을 사용하지 않고도 해결할 길이 있다.
바로 와일드카드 타입의 실제 타입을 알려주는 메서드를 private 도우미 메서드로 따로 작성하여 활용하는 방법이다.

```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j);
}

private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```

swapHelper 메서드는 리스트가 List<E> 임을 알고 있다. 
즉 리스트에서 꺼낸 값의 타입은 항상 E이고, E 타입의 값이라면 이 리스트에 넣어도 안전함을 알고 있다.