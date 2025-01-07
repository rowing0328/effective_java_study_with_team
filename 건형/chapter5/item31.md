## 📖 한정적 와일드카드를 사용해 API 유연성을 높이라

### 핵심 내용

- 조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해진다.

- 그러니 널리 쓰일 라이브러리를 작성한다면 반드시 와일드카드 타입을 적절히 사용해줘야 한다.

- PECS 공식을 기억하다!

- 즉, 생산자(producer)는 extends를 소비자(consumer)는 super를 사용한다.

- Comparable 과 Comparator는 모두 소바자하는 사실도 잊지 말자

## 💡 주요 내용 정리 & 🛠️ 실습 코드

```java
public class Stack<E> {
    public Stack() {}

    public void push(E e) {}
    
    public E pop() {}
    
    public boolean isEmpty();
}
```

와일드카드 타입을 사용하지 않는 pushAll 메서드 - 결함이 있다!
```java
public void pushAll(Iterable<E> src) {
    for (E e : src) {
        push(e);
    }
}
```

Iterable src의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다.

하지만 Stack<Number>로 선언 후 pushAll(intVal)을 호출하게 된다면? 

매개변수화 타입이 불공변이기 때문에 오류 메시지가 발생한다.

이런 상황을 해결하기 위해서 한정적 매개변수화 타입을 사용하면 된다.

E 생산자 (producer) 매개변수에 와일드카드 타입 적용
```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}
```

와일드키드 타입을 사용하지 않은 popAll 메서드 - 결함이 있다!
```java
public void popAll(Collection<E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

E 소비자(consumer) 매개변수에 와일드카드 타입 적용
```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

**유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라!**

입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을게 없다.

타입을 정확히 지정해야 하는 상황으로, 이때는 와일드카드 타입을 쓰지 말아야 한다.

### PECS : producer-extends, consumer-super

매개변수화 타입 T가 생산자라면 <? extends T>를 사용하고, 소비자라면 <? super T>를 사용하다.

Stack 에에서 pushAll의 src 매개변수는 Stack이 사용할 E 인스턴스를 생산하므로 src의 적절한 타입은

Iterable<? extends E>이다.

한편, popAll의 dst 매개변수는 Stack으로부터 E 인스턴스를 소비하므로 dst의 적절한 타입은 Collection<? super E>이다.

> 반환 타입에는 한정적 와일드카드 타입을 사용하면 안 된다.
> 유연성을 높여주기는커녕 클라이언트 코드에서도 와일드카드 타입을 써야 하기 때문이다.

- ? extends T: 데이터를 읽는 메서드에 적합하며, 생산자의 역할을 합니다.

- ? super T: 데이터를 추가하거나 수정하는 메서드에 적합하며, 소비자의 역할을 합니다.

- PECS 원칙은 제네릭 타입 설계에서 유연성과 안정성을 높이는 핵심입니다.


한정적 와일드카드 타입으로 선언한 예시
```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2);

// 한정적 와일드카드 타입으로 선언하면 아래 코드가 말끔히 컴파일 된다.
Set<Integer> integers = Set.of(1, 3, 5);
Set<Double> doubles = Set.of(2.0, 4.0, 6.0);
Set<Number> numbers = union(integers, doubles);

// 자바 7 까지는 명시적 타입 인수를 사용해야 했다.
Set<Number> numbers = Union.<Number>union(integers, doubles);
```

**클래스 사용자가 와일드카드 타입을 신경 써야 한다면 그 API에 무슨 문제가 있을 가능성이 크다.**


### Comparable<E>보다는 Comparable<? super E>를 사용하는 편이 낫다.
### Comparator<E>보다는 Comparator<? super E>를 사용하는 편이 낫다.


swap 메서드의 두 가지 선언
```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

**메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라!**

이때 비한정적 타입 매개변수하면 비한정적 와일드카드로 바꾸고, 한정적 타입 매개변수하면 한정적 와일드카드로 바꾸면 된다.


## 추가 강의 내용 정리

### Bounded Wildcard 설명을 위한 예제 코드

```java
public class Stack<E> {
    public static final int DEFAULT_SIZE = 20;
    private int size;
    private E[] elements;
    public Stack(){
        elements = (E[]) new Object[DEFAULT_SIZE];
        size = 0;
    }

    public E push(E item) {
        elements[size++] = item;
        return item;
    }

    public void pushAll(Iterable<E> src) {
        for (E e : src) {
            push(e);
        }
    }
}
```

### Bounded Wildcard example

컴파일 에러 : 불공변이기 때문 (invariant)

자기 타입만 허용

해결책은??

제네릭 (E) 를 extends 한 와일드카드를 입력으로 받겠다!

제네릭 상위 클래스인 와일드카드를 Collection으로 받겠다!


### 정리

Bounded Wildcard를 알고는 있지만 자세히 들여다 본 적은 없을 확률이 높다.


## 🤔 생각 정리

- 이번 장에서 학습한 내용을 토대로 각종 라이브러리 코드를 분석하는 연습을 하면 유용하게 사용할 수 있을 것이라고 생각했습니다.
