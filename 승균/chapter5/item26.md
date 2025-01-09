## item26: 로 타입은 사용하지 말라

## **핵심 주제**

클래스와 인터페이스 선언에 타입 매개변수(type parameter)가 쓰이면, 제네릭 클래스 혹은 제네릭 인터페이스라 한다.

예컨대 List 인터페이스는 원소의 타입을 나타내는 타입 매개변수 E를 받는다. <br/>
이 인터페이스의 완전한 이름은 List<E>지만, 짧게 그냥 List라고 쓰임

제네릭 클래스와 제네릭 인터페이스를 통틀어 **제네릭 타입**이라고 함.

각각의 제네릭 타입은 일련의 매개변수화 타입(parameterized type)을 정의한다.<br/>
먼저 클래스(혹은 인터페이스) 이름이 나오고, 이어서 꺾쇠괄호 안에 실제 타입 매개변수들을 나열한다.

제네릭 타입을 하나 정의하면 그에 딸린 로 타입(raw type)도 함께 정의된다. <br/>
로 타입이란 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때를 말한다. 

예컨대 List<E>의 로 타입은 List다. 로 타입은 타입 선언에서 제네릭 타입 정부가 전부 지워진 것처럼 동작하는데, 제네릭이 도래 하기 전 코드와 호환되도록 하기 위한 궁여지책

**컬렉션의 로타입 - 따라 하지 말것!**
```java
// Stamp 인스턴스만 취급한다.
private final Collections stamps = ...;
```

이 코드를 사용하면 실수로 stamp 대신 coin을 넣어도 아무 오류 없이 컴파일되고 실행된다.(컴파일러가 모호한 경고 메시지를 보여주긴함)

```java
// 실수로 동전을 넣는다. 
stamp.add(new Coin(...)); // "unchecked call" 경고를 내뱉는다. 
```

컬렉션에서 동전을 다시 꺼내기 전까지는 오류를 알아차리지 못한다.

**반복자의 로 타입 - 따라 하지 말것!**
```java
for(Iterator i = stamps.iterator(); i.hasNext()) {
    Stamp stamp = (Stamp) i.next();
    stamp.cancel();
}
```

오류는 가능한 발생 즉시, 이상적으로는 컴파일에서 발견하는 것이 좋다. <br/> 
위에 예시는, 오류가 발생하고 한참 뒤인 런타임에야 알아 차릴 수 있음 <br/>

ClassCastException 발생하면 stamps에 동전을 넣은 지점을 찾기 위해 코드 전체를 훑어봐야 한다.

제네릭을 활용하면 이 정보가 주석이 아닌 타입 선언 자체에 녹아든다.

**매개변수화된 컬렉션 타입- 타입 안정성 확보!**
```java
private final Collection<Stamp> stamps = ...;
```

컴파일러는 stamps에는 Stamp 인스턴스만 넣어야 함을 컴파일러가 인지하게 된다. <br/>
따라서 아무런 경고 없이 컴파일된다면 의도대로 동작할 것임을 보장한다.

로 타입을 쓰는걸 언어 차원에서 막지 않았지만 절대로 써서는 안 된다. <br/>
**로 타입을 쓰면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게 된다.**

List 같은 로 타입 x , but List<Object>처럼 임의 객체를 허용하는 매개변수화 타입은 괜찮다. <br/>
로 타입인 List와 매개변수화 타입인 List<Object> 차이는 무엇일까?

List는 제네릭 타입에서 완전히 발을 뺀 상태, List<Object> 모든 타입을 허용한다는 컴파일러에게 명확한 의사를 전달하냐 전달하지 않냐 차이

**런타임에 실패한다. - unsafeAdd 메서드가 로 타입(List)을 사용**
```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0);
}

private static void unsafeAdd(List list, Object o) {
    list.add(o);
}
```

위에를 실행 시키면 strings.get(0) 결과를 형변환하려 할 때, ClassCastException 던진다. <br/>
Integer를 String 변환하려 시도한 것이다.

이제 로타입인 List를 매개변수화 타입인 List<Object>로 바꾼 다음 컴파일 

오류메세지 출력되며, 컴파일 조차 되지 않음.

**잘못된 예- 모르는 타입의 원소도 받는 로타입을 사용했다.**
```java
static int numElementInCommon(Set s1, Set s2) {
    int result = 0;
    for (Object o1 : s1) 
        if(s2.contains(o1)) result++;
    return result;
}
```

이 메서드는 동작은 하지만, 로 타입을 사용해 안전하지 않다. 따라서 비한정적 와일드카드 타입을 대신 사용하는게 좋다.

제네릭 타입을 쓰고 싶지만 실제 타입 매개변수가 무엇인지 신경 쓰고 싶지 않다면 물음표(?) 사용하자.

애컨대 제네릭 타입인 Set<E>의 비한정적 와일드카드 타입은 Set<?> 다.

**비한정적 와일드카드 타입을 사용하라. - 타입 안전하며 유연하다.**
```java
static int numElementInCommon(Set<?> s1, Set<?> s2) { ... }
```

비한정적 와일드 카드 타입인 Set<?>와 로 타입인 Set의 차이 무엇?<br/>
와일드카드 타입은 안전, 로타입은 안전하지 않음.

로타입 컬레션에는 아무 원소나 넣을 수 있으니 타입 불변식을 훼손하기 쉬움.

반면, Collection<?> (null외에는) 어떤 원소도 넣을 수 없다.


**class 리터럴에는 로 타입을 써야 한다.**

자바 명세는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했다.

List.class, String[].class, int.class는 허용하고 List<String>.class와 List<?>.class는 허용하지 않는다.

**로 타입을 써도 좋은 예 - instanceof 연산자**
```java
if (o instanceof Set) {
    Set<?> s = (Set<?>) o;
    ...
}
```

