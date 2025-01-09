## 제네릭과 가변인수를 함께 쓸 때는 신중하라.

## **핵심주제**
가변인수(varargs) 메서드와 제네릭은 자바 5 때 함께 추가되었으니 서로 잘 어우러지리라 기대하겠지만, 슬프게도 그렇지 않다. 

가변인수는 메서드에 넘기는 인수의 개수를 클라이언트가 조절할 수 있게 해주는데, 구현 방식에 허점이 있다.

가변인수 메서드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어진다. 

거의 모든 제네릭과 매개변수화 타입은 실체화되지 않는다. <br/> 
메서드를 선언할 때 실체화 불가 타입으로 varargs 매개 변수를 선언하면 컴파일러가 경고를 보낸다. <br/>


매개변수화 타입이 변수가 다른 객체를 참조하면 힙 오염이 발생한다. 

이렇게 다른 타입 객체를 참조하는 상황에서는 컴파일러가 자동 생성한 형변환이 실패할 수 있으니, 제네릭 타입 시스템이 약속한 타입 안정성의 근간이 흔들려버린다. 

## **제네릭과 varargs 혼용하면 타입 안정성이 깨진다.**

```java
static void dangerous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;
    objects[0] = intList; // 힙 오염 발생
    String s = stringLists[0].get(0); // ClassCastException
}
```

타입 안정성이 깨지니 제네릭 varargs 배열 매개변수에 값을 저장하는 것은 안전하지 않다. 

제네릭 배열을 프로그래머가 직접 생성하는 건 허용하지 않음. 제네릭 varargs 매개변수를 받는 메서드를 선언할 수 있게 한 이유가 무엇일까?

달리 말하면, 코드 28-3은 오류를 내면서 32-1은 경고로 끝내는 이유가 뭘까? 

그 답은 제네릭이나 매개변수화 타입 varargs 매개변수를 받는 메서드가 실무에서 매우 유용하기 때문이다. << 언어의 모순을 수용하기로 함. 

자바 라이브러리도 이런 메서드를 여럿 제공하는데, Arrays.asList(T... a) , Collections.addAll(Collection<? super T> c, T... elements), EnumSet.of(E first, E... rest)가 대표적이다. 

@SuppressWarnings("unchecked") 애너테이션을 달아 경고를 숨겨야 했다. 지루한 작업이고, 가독성을 떨어뜨리고, 떄로는 진짜 문제를 알려주는 경고 마자 숨겨 버렸음. 

@SafeVarargs 애너테이션이 추가되어 제네릭 가변인수 메서드 작성자가 클라이언트 측에서 발생하는 경고를 숨길 수 있게 되었다. 

메서드가 안전한 게 확실하지 않다면 절대 **@SafeVarargs 애너테이션은 작성자가 그 메서드가 타입 안전함을 보장하는 장치**이다.

메서드가 안전한지? 어떻게 확신?
매개변수 배열이 호출자로부터 그 메소드로 순수하게 인수들을 전달하는 일만 한다면(varargs의 목적대로만 쓰인다면) 그 메서드는 안전하다.

이때, varargs 매개변수에 배열에 아무것도 저장하지 않고도 타입 안전성을 깰수도 있으니 주의해야 한다. <br/>

**자신의 제네릭 매개변수 배열의 참조를 노출한다. - 안전하지 않다!**

```java
static <T> T[] toArray(T... args) {
    return args;
}
```

메서드가 반환하는 배열의 타입은 메서드에 인수를 넘기는 컴파일타임에 결정되는데, 그 시점에는 컴파일러에게 충분한 정보가 주어지지 않아 타입을 잘못 판단할 수 있다. <br/>
따라서 자신의 varargs 매개변수 배열을 그대로 반환하면 힙 오염을 이 메서드를 호출한 쪽의 콜스택으로까지 전이하는 결과를 낳을 수 있다. <br/>

```java
static <T> T[] pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return toArray(a, b);
        case 1: return toArray(a, c);
        case 2: return toArray(b, c);
    }
    throw new AssertionError();
}
```

이 메서드를 본 컴파일러는 toArray에 넘길 T 인스턴스 2개를 담을 varargs 매개변수 배열을 만드는 코드를 생성한다.

이 코드가 만드는 배열의 타입은 Object[]인데, pickTwo에 어떤 타입의 객체를 넘기더라도 담을 수 있는 가장 구체적인 타입이기 때문이다.

toArray 메서드가 돌려준 이 배열이 그대로 pickTwo 호출한 클라이언트까지 전달된다. 즉, pickTwo 항상 Object[] 타입 배열을 반환한다.

이제 pickTwo를 사용하는 main 메서드를 볼 차례이다.

```java
public static void main(String[] args) {
    String[] attributes = pickTwo("좋은", "빠른", "저렴한");
}
```

실행할 때, ClassCastException 던진다. 

바로 pickTwo 반환 값을 attributes에 저장하기 위해 String[]로 형변환하는 코드를 컴파일러가 자동 생성한다는 점을 놓쳤다. 

Object[]는 String[] 하위 타입이 아니므로 이 형변환은 실패한다. <br/>

이 실패가 다소 황당하게 느껴질 수 있을 것이다. 힙 오염을 발생시긴 진짜 원인인 toArray로 부터 2단계 떨어져 있기 때문이다. 

varargs 매개변수 배열은 실제 매개변수가 저장된 후, 변경되지 않았기 때문이다. 

제네릭 varargs 매개 변수 배열에 다른 메서드가 접근하도록 허용하면 안전하지 않다는 점을 다시 한번 상기 시킨다. 

제네릭 varags 매개변수를 안전하게 사용하는 전용적인 예는 아래 코드이다. 

**제네릭 varargs 매개변수를 안전하게 사용하는 메서드**
```java
@SafeVarargs
static <T> List<T> flatten(List<? extends T> ... list) {
    List<T> result = new ArrayList<>();
    for(List<? extends T> list : lists) 
        result.addAll(list);
    return result;
}
```

@SafeVarargs 애너테이션을 사용해야 할 때는 정하는 규칙은 간단하다.

제네릭이나 매개변수화 타입의 varargs 매개변수는 받는 모든 메서드에 @SafeVarargs를 달라. 

- varargs: 매개변수 배열에 아무것도 저장하지 않는다.
- 그 배열(혹은 복제본)을 신뢰할 수 없는 코드에 노출하지 않는다. 

```
@SafeVarargs 애너테이션은 재정의 할 수 없는 메서드에만 달아야 한다.
재정의한 메서드도 안전할지는 보장할 수 없기 때문이다. 자바 8에서 이 애너테이션은 오직 정적 메서드와 final 인스턴스 메서드에만 붙일 수 있고, 
자바 9부터는 private 인스턴스 메서드에도 허용된다. 
```

@SafeVarargs 애너테이션이 유일한 정답은 아니다. (실체는 배열인) varargs 매개변수를 List 매개변수로 바꿀 수도 있다. 

**제네릭 varargs 매개변수를 List로 대체한 예 - 타입 안전하다**
```
static <T> List<T> flatten(List<List<? extends T>> lists) {
    List<T> result = new ArrayList<>();
    for(List<? extends T> list : lists)
        result.addAll(list);
    return result;
```

정적 팩터리 메서드인 List.of를 활용하면 다음 코드와 같이 이 메서드에 임의개수의 인수를 넘길 수 있다. 

List.of에도 @SafeVarargs 애너테이션이 달려있기 때문이다.
```java
audience = flatten(List.of(friends, romans, countrymen));
```

이 방식은 코드 32-2 toArray처럼 varargs 메서드를 안전하게 작성하는게 불가능한 상황에서도 사용할 수 있음. 

toArray의 List버전이 바로 List.of로, 자바 라이브러리 차원에서 제공하니 우리가 직접 작성할 필요도 없다. 
```java
static <T> List<T> pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return List.of(a, b);
        case 1: return List.of(a, c);
        case 2: return List.of(b, c);
    }
    throw new AssertionError();
}
```

그리고 main 메서드는 다음처럼 변한다.

```java
public static void main(String[] args) {
    List<String> attributes = pickTwo("좋은", "빠른", "저렴한");
}
```
