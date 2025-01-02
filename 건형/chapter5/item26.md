## 제네릭 (Generic)

제네릭(generic)을 지원하기 전에는 컬렉션에서 객체를 꺼낼 때마다 형변환을 해야 했다.

그래서 누군가 실수로 엉뚱한 타입의 객체를 넣어두면 런차임에 형변환 오류가 나곤 했다.

반면, 제네릭을 사용하면 컬렉션이 담을 수 있는 타입을 컴파일러에 알려주게 된다.

그래서 컴파일러는 알아서 형변환 코드를 추가할 수 있게 되고, 엉뚱한 타입의 객체를 넣으려는 시도를 컴파일 과정에서 차단하여 더 안전하고 명확한 프로그램을 만들어 준다.

## 용어 정리

| 한글 용어        | 영문 용어 | 예                                | 
|:-------------|:-------|:---------------------------------|
| 매개변수화 타입     | parameterized type | List<String>                     |
| 실제 타입 매개 변수  | actual type parameter | String                           |
| 제네릭 타입       | generic type | List<E>                          |
| 정규 타입 매개변수   | formal type parameter | E                                |
| 비한정적 와일드카드 타입 | unbouned wildcard type | List<?>                          |
| 로 타입         | raw type | List                             |
| 한정적 타입 매개변수  | bounded type parameter | <E extends Number\>              |
| 재귀적 타입 한정    | recursive type bound | <T extends Comparable<T>>        |
| 한정적 와일드카드 타입 | bounded wildcard type | List<? extends Number>           |
| 제네릭 메서드      | generic method | static <E> List<E> asList(E[] a) |
| 타입 토큰        | type token | String.class                     |


## 📖 로 타입은 사용하지 말자

### 핵심 내용

- 로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안 된다. 
- 로 타입은 제네릭이 도입되기 이전 코드와의 호환성을 위해 제공될 뿐이다.
- Set<Object>는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이고,
- Set<?>는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다.
- 로타입인 Set은 제네릭 타입 시스템에 속하지 않는다.

**Set<Object>와 Set<?>는 안전하지만, 로 타입인 Set은 안전하지 않다.**

## 💡 주요 내용 정리 & 🛠️ 실습 코드
- 주요 내용 정리

- 클래스와 인터페이스 선언에 타입 매개변수가 쓰이면, 이를 제네릭 클래스 혹은 제네릭 인터페이스라 합니다.

- 제네릭 클래스와 제네릭 인터페이스를 통틀어 제네릭 타입이다 한다.

- 제네릭 타입은 일련의 매개변수화 타입을 정의한다.
    - 먼저 클래스(혹은 인터페이스) 이름이 나오고, 이어서 꺾쇠괄호 안에 실제 타입 매개변수들을 나열한다.
    - 예컨데 List<String>은 원소의 타입인 String인 리스트를 뜻하는 매개변수화 타입이다.
    - 여기서 String이 정규 타입 매개변수 E에 해당하는 실제 타입 매개변수다.

  - 제네릭 타입을 하나 정의하면 그에 딸린 로 타입 (raw type)도 함께 정의된다.
      - 로 타입이란 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때를 말합니다.
      - Ex) List<E>의 로 타입은 List
      - 컬렉션의 로 타입 - 따라 하지 말 것!
      ```java
      // Stamp 인스턴스만 취급한다.
      private final Collection stamps = ...;
    
      // 실수로 동전을 넣는다.
      stamps.add(new Coin(...));
      ```
      - 위 코드를 사용하면 실수로 도장 대신 동전을 넣어도 아무 오류없이 컴파일되고 실행됩니다.
      - 이런 경우 컬렉션에서 이 동전을 다시 꺼내지 전에는 오류를 알아채지 못합니다.
  
    ```java
    for (Iterator i = stamps.iterator(); i.hasNext();) {
        Stamp stamp = (Stamp) i.next(); // ClassCastException을 던진다.
        stamp.cancel();
    }
    ```
    
- 오류는 가능한 한 발생 즉시, 이상적으로는 컴파일할 때 발견하는 것이 좋다.

- 오류가 발생하고 한참 뒤 런타임에서 오류 발생여부를 알아채게 되면 문제를 겪는 코드와 원인을 제공한 코드가 물리적으로 상당히 떨어져 있을 가능성이 커진다.

- 이렇게 되면 오류 코드를 찾기 위해 훑어봐야 하는 코드의 범위가 전체로 늘어나게 된다.


### 제네릭을 활용하여 타입 정보를 타입 선언 자체에 녹이자

```java
private final Collection<Stamp> stamps = ...;
```

- 위와 같이 선언하면 컴파일러는 stamps에는 Stamp의 인스턴스만 넣어야 함을 컴파일러가 인지하게 됩니다.

- 따라서 아무런 경고 없이 컴파일된다면 의도대로 동작할 것임을 보장합니다.

- 컴파일러는 컬렉션에서 원소를 꺼내는 모든 곳에 보이지 않는 형변환을 추가하여 절대 실패하지 않음을 보장한다.


### 로 타입(타입 매개변수가 없는 제네릭 타입)을 쓰는 걸 언어 차원에서 막아 놓지는 않았지만 절대로 써서는 안된다!

**로 타입을 쓰면 제네릭이 안겨주는 안정성과 표현력을 모두 잃게 된다.**

- 단지, 로 타입을 남겨 놓은 이유는 호환성 때문이다.


### List 같은 로 타입은 사용해서는 안 되나, List<Object>처럼 임의 객체를 허용하는 매개변수화 타입은 괜찮다!

**List 와 List<Object> 의 차이?**

- List 는 제네릭 타입에서 완전히 발을 뺀 것

- List<Object>는 모든 타입을 허용한다는 의사를 컴파일러에 명확히 전달한 것

**List<Object> 같은 매개변수화 타입을 사용할 때와 달리 List 같은 로 타입을 사용하면 타입 안전성을 잃게 된다.**

런타임에 실패한다. - unsafeAdd 메서드가 로 타입(List) 사용
```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0); // 컴파일러가 자동으로 형변환 코드를 넣어준다.
}

private static void unsafeAdd(List list, Object o) {
    list.add(o);
}
```
- 위 코드는 컴파일은 되지만 로 타입인 List를 사용하여 경고가 발생한다. 
- 해당 코드를 그대로 실행하면 string.get(0)의 결과를 형변환하려 할 때 ClassCastException을 던진다.


잘못된 예 - 모르는 타입의 원소도 받는 로 타입을 사용했다.
```java
static int numElementsInCommon(Set s1, Set s2) {
    int result = 0;
    for (Object o1 : s1) {
        if (s2.contains(o1)) {
            result++;
        }
        return result;
    }
}
```

- 위 메서드는 동작은 하지만 로 타입을 사용해 안전하지 않습니다.

- 따라서 비한정적 와일드카드 타입 (unbounded wildcard type)을 대신 사용하는 게 좋습니다.

**제네릭 타입을 쓰고 싶지만 실제 타입 매개변수가 무엇인지 신경 쓰고 싶지 않다면 물음표(?)를 사용하자.**

비한정적 와일드카드 타입을 사용하라. - 타입 안전하며 유연하다.
```java
static int numElementInCommon(Set<?> s1, Set<?> s2) { ... }
```

**비한정적 와일드카드 타입인 Set<?>와 로 타입인 Set의 차이?**

- 화일드 타입은 안전하고, 로 타입은 안전하지 않다.
- 로 타입 컬렉션에는 아무 원소나 넣을 수 있어 타입 불변식을 훼손하기 쉽다.
- 반면, Collection<?>에는 (null 외에는) 어떤 원소도 넣을 수 없다. 다른 원소를 넣으려 하면 컴파일 시에 오류 메세지를 보게 된다.

### 로 타입을 쓰지말라는 규칙의 예외!

1. class 리터럴에는 로 타입을 써야 한다.
- 자바 명세는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했다. (배열과 기본 타입은 허용한다.)
- 예를 들어 List.class, String[].class, int.class는 허용하고 List<String>.class와 List<?>.class는 허용하지 않는다.

2. 런타임에는 제네릭 타입 정보가 지워지므로 instanceof 연산자는 비한정적 와일드카드 타입 이외의 매개변수화 타입에는 적용할 수 없다.
- 로 타입이든 비한정적 와일드카드 타입이든 instanceof는 완전히 똑같이 동작한다.

로 타입을 써도 좋은 예 - instanceof 연산자
```java
if (o instanceof Set) { // 로 타입
    Set<?> s = (Set<?>) o; // 와일드카드 타입
        }
```

> 0의 타입이 Set임을 확인한 다음 와일드카드 타입인 Set<?>로 형변환해야 한다(로 타입인 Set이 아니다.)
>
> 이는 검사 형변환(checked cast)이므로 컴파일러 경고가 뜨지 않는다.


## 추가 강의 내용 정리

만약 type parameter 가 없다면? 
```java
List test = new ArrayList<>();
test.add("no1");
test.add(1);
```

unbounded wildcard type 사용하는 것을 권장
```java
private int add(List<?> s1) {
  s1.add(1); // error!
  return 1;
}
```

### 명확하게 타입 추론이 가능하게 하자
그렇지 않다면 런타임 시 예외가 발생할 수 있다.

List<Object>는 명확하게 내가 Object라는 타입을 제시한 것임. 오해 X

하단과 같이 merge해 버린다면, 다른 타입이 들어왔을 때 런타임 시 언젠가는 문제가 생긴다.

```java
static List listMerge(List a, List b) {
  List c = new ArrayList();
  c.addAll(a);
  c.addAll(b);
  return c; // 잘못된 예
}
```

### 예외적인 사용 예

Class 리터럴에는 로 raw type을 써야 한다.

```java
List.class
```

런타임 시 제네릭 타입 정보가 지워지기 때문에 다음과 같이 체크
```java
if( a instanceof Set ) {
    Set<?> s = (Set<?>) a;
    for(Object o : s){
        if(o instanceof String){
            // true
        }
    }
}
```

### Generic Class / Generic Interface

```java
List<String> test = new ArrayList<>();

// List: Generic Interface
public interface List<E> extends Collection<E> {}

// ArrayList: Generic class
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {}
```

## 정리

예외적인 케이스를 제외하면 명확히 타입을 명시하도록 하자.

## 🤔 생각 정리
- 궁금한 점, 논의하고 싶은 내용
