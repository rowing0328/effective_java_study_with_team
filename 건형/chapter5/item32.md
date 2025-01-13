## 📖 제네릭과 가변인수를 함께 쓸 때는 신중하라

### 핵심 내용

가변인수와 제네릭은 궁합이 좋지 않다.

가변인수 기능은 배열을 노출하여 추상화가 완벽하지 못하고, 배열과 제네릭의 타입 규칙이 서로 다르기 때문이다.

제네릭 varargs 매개변수는 타입 잔전하지는 않지만, 허용된다.

메서드에 제네릭 (혹은 매개변수화된) varargs 매개변수를 사용하고자 한다면, 먼저 그 메서드가 타입 안전한지 확인한 다음 @SafeVarargs 애너테이션을 달아 사용하는 데 불편함이 없게끔 하자.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

자신의 제네릭 매개변수 배열의 참조를 노출한다. - 안전하지 않다!
```java
static <T> T[] toArray(T... args) {
    return args;
}
```

제네릭 varargs 매개변수 배열에 다른 메서드가 접근하도록 허용하면 안전하지 않다!

**예외**

1. @SafeVarargs로 제대로 애노테이트된 또 다른 varargs 메서드에 넘기는 것은 안전하다.
2. 그저 이 배열 내용의 일부 함수를 호출만 하는 (varargs를 받지 않는) 일반 메서드에 넘기는 것도 안전하다.

제네릭 varargs 매개변수를 안전하게 사용하는 메서드
```java

@SafeVarargs
static <T> List<T> flatten(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists) {
        result.addAll(list);
    }
    return result;
}
```

@SafeVarargs 애너테이션을 사용해야 할 때를 정하는 규칙?

-> **제네릭이나 매개변수화 타입의 varargs 매개변수를 받는 모든 메서드에 @SafeVarargs를 달라**

다음 두 조건을 모두 만족하는 제네릭 varargs 메서드는 안전하다

1. varargs 매개변수 배열에 아무것도 저장하지 않는다.
2. 그 배열 (혹은 복제본)을 신뢰할 수 없는 코드에 노출하지 않는다.

> @SafeVarargs 애너테이션은 재정의할 수 없는 메서드에만 달아야 한다. 
>
> 재정의한 메서드도 안전할지는 보장할 수 없기 때문이다.
> 
> 자바 8에서 이 애너테이션은 오직 정적 메서드와 final 인스턴스 메서드에만 붙일 수 있고,
> 
> 자바 9부터는 private 인스턴스 메서드에도 허용된다.


## 추가 강의 내용 정리

### Variadic Arguments (가변 인수)

- Method의 argument의 개수를 클라이언트가 조절할 수 있게 한다.
- 또한 반드시 **한개의** 가변 인수만을 사용해야 하며 **맨 마지막 Argument**로 사용해야 한다.

```java
static void mergeAll(List<String>... stringLists) {}
```

- 위의 코드를 풀어 쓰자면 다음과 같은 의미이고
- (아래 코드는 컴파일 불가, 컴파일 가능하려면 List[] stringLists = {one,two,three});
```java
static void mergeAll(List<String> one, List<String> two, List<String> three) {
    List<String>[] stringLists = {one, two, three};
}
```

### Heap Pollution (힙 오염)

실체화가 안되는 타입들이 파라미터로 들어올 때 힙 영역이 오염될 가능성이 있다.

```java
static <T> List<T> flattern(List<? extends T>... lists) {
    return null;
}
```
한정적 와일드카드 타입의 리스트를 가변 인수로 받게되면 힙 오염이 발생할 수 있으니 주의해야 한다!

```java
List[] test = {List.of(1), List.of(2)}; // True
List<Integer>[] test2 = {List.of(1), List.of(2)}; // Error
```

안전하기 위해선 제네릭 배열에 아무것도 저장하거나 덮어쓰지 말고, 배열의 참조를 밖으로 노출시키지 말아야 한다!

**가변 인수를 사용할 때에는 힙 오염을 항상 주의해야 합니다!!!** 


### Remove Warning

@SuppressWarnings (컴파일 경고 숨기기)

@SafeVarargs (메서드의 타입 안전성을 보장함)


### 정리

제네릭 배열에 아무것도 저장하거나 덮어쓰지 말고,
배열의 참조를 밖으로 노출시키지 말아야 한다.

제네릭과 가변인수를 함께 사용할 때에는 궁합이 잘 맞지 않으니 조심하자

아니면 이중 리스트 같은 것도 해답이 될 수 있다. 


## 🤔 생각 정리

- 해당 장은 이해가 잘 가지 않았지만, 기억하고 넘어가야 하는 것은 제네릭은 런타임에서는 타입 정보가 소거되기 때문에 
- 타입 안전성을 보장할 수 없는 상황이 발생하지 않는 코드를 작성해야 된다는 점이다!

