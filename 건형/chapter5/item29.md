## 📖 이왕이면 제네릭 타입으로 만들라

### 핵심 내용

- 클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편하다.

- 그러니 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라.

- 그렇게 하면 제네릭 타입으로 만들어야 할 경우가 많다.

- 기존 타입 중 제네릭이었어야 하는 게 있다면 제네릭 타입으로 변경하자.

- 기존 클라이언트에는 아무 영향을 주지 않으면서, 개로운 사용자를 훨씬 편하게 해주는 길이다.


## 💡 주요 내용 정리 & 🛠️ 실습 코드

Object 기반 스택 - 제네릭이 절실한 강력 후보
```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

### 일반 클래스를 제네릭 클래스로 만드는 첫 단계는 클래스 선언에 타입 매개변수를 추가하는 일

```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public E pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        E result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }
    // ... 
    // isEmpty와 ensureCapacity 메서드는 그대로다.
}
```

- E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.


- 해결책
1. 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법
     - 가독성이 좋다.
     - 배열의 타입을 E[]로 선언하여 오직 E타입 인스턴스만 받을을 확실히 어필한다.
     - 코드도 더 짧다. 형변환을 배열 생성 시 한번만 해주면 된다.
     - 다만, E다 Object가 아닌 한 배열의 런타임 타입이 컴파일타임 타입과 달라 힙 오염을 일으킨다.

2. elements 필드의 타입을 E[]에서 Object[]로 바꾸는 방법


## 추가 강의 내용 정리

### Type parameter Naming Conventions

E - Element
K - key
N - Number
T - Type
V - Value
S, U, V etc - 2,3,4 type

Type parameter를 사용한 예

```java
@NoRepositoryBean
public interface JpaRepository<T, ID> {}
```

### Generic Type Example

```java
public class Calculator<E> {
    private StringBuilder expression;
    public Calculator() {
        expression = new StringBuilder();
    }
    public void add(E e) {
        expression.append("+" + e.toString());
    }
    public void minus(E e) {
        expression.append("-" + e.toString());
    }
    public String expression() {
        if (expression.charAt(0) == '+'){
            return expression.substring(1);
        } else {
            return expression.toString();
        }
    }
    
}
```

### 정리

- Object를 이용한 직접 형변환하는 코드 대신 제네릭 타입으로 만들어 형변환하는 코드를 없애라.

- 혹시나 기존 형변환 코드가 있다면, 리팩토링 강력 추천!!

## 🤔 생각 정리

- 숨쉬듯이 당연하게 제네릭으로 만드는 것이 좋다.

