## item29: 이왕이면 제네릭 타입으로 만들라

## **핵심 주제**

**Object 기반 스택- 제네릭이 절실한 강력 후보!**
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
        if(size == 0) throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    private void ensureCapacity() {
        if(elements.length == size) 
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

해당 클래스를 제네릭으로 바꿔보자!

**제네릭 스택으로 가능 첫 단계 - 컴파일되지 않는다.**

```java
import java.util.EmptyStackException;

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
        if (size == 0) throw new EmptyStackException();
        E result = elements[--size];
        elements[size] = null;
        return result;
    }
    
    ... // isEmpty와 ensureCapacity()메소드 그대로
}
```

elements = new E[DEFAULT_INITIAL_CAPACITY]; => E는 실체화 불가 타입이므로 배열을 만들 수 없다.

배열로 사용하는 코드를 제네릭으로 만들려 할 때는 항상 이 문제가 발목을 잡는다. 

적절한 해결책은 두 가지이다.
1. 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법이다.

object 배열을 생성한 다음 제네릭 배열로 형변환해보자.
```java
Stack.java8: warning: [unchecked] unchecked cast
found: Object[], required: E[]
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY)]
```

컴파일러는 이 프로그램이 타입이 안정한지 증명할 방법이 없지만 우리는 위에 처럼 코드를 작성 할 수 있다. 

대신에 직접 프로그램의 타입 안정성을 해치지 않는 다는 것을 파악해야함.

**배열을 사용한 코드를 제네릭으로 만드는 방법1**
```java
// 배열 elements는 push(E)로 넘어온 E 인스턴스만 담는다.
// 따라서 타입 안전성을 보장하지만, 
// 이 배열의 런타임 타입은 E[]가 아닌 Object[]다!!
@SuppressWarnings("unchecked")
public Stack() {
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
}
```

2. 두번째 방법
elements 필드의 타입을 E[]에서 Object[]로 바꾸는 것이다. 이렇게 하면 첫 번째와는 다른 오류가 발생

```java
import java.util.EmptyStackException;//비검사 경고를 적절히 숨긴다.
public void pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    
    // push에서 E 타입만 허용하므로 이 형변환은 안전하다.
    @SuppressWarnings("unchecked") E result = (E) elements[--size];
    
    elements[size] = null;
    return result;
}
```
제네릭 배열 생성을 제거하는 두 방법 모두 나름의 지지를 얻고 있다. 첫번째 방법은 가독성이 좋다. <br/>
배열의 타입을 E[]로 선언하여 오직 E타입 인스턴스만 받음을 확실히 어필함. <br/>
제네릭 클래스라면 코드 이곳저곳에서 이 배열을 자주 사용할 것이다. <br/>
첫 번째 방식에서는 형변환을 배열 생성 시 단 한 번만 해주면 되지만, 두 번째 방식에서는 배열에서 원소를 읽을 때마다 해줘야함. <br/>
따라서 현업에서는 첫 번째 방식을 더 선호 한다. but 런타임 타입이 컴파일 타입과 달라 힙 오염을 일으키기도 함. 이런 경우 두번쨰 방법 쓰기도함.

**제네릭 Stack을 사용하는 맛보기 프로그램**
```java
public static void main(String[] args) {
    Stack<String> stack = new Stack<>();
    for(String arg : args) {
        stack.push(arg);
    }
    while(!stack.isEmpty()) {
        System.out.println(stack.pop().toUpperCase());
    }
}
```

제네릭 타입 안에서 리스트를 사용하는게 항상 가능하지도, 꼭 좋은 것도 아니다. 

자바 리스트를 기본 타입으로 제공하지 않으므로 ArrayList 같은 제네릭 타입도 결국은 기본 타입인 배열을 사용해 구현해야 한다.

결국은 기본 타입인 배열을 사용해 구현해야함. 또한 HashMap 같은 제네릭 타입은 성능을 높일 목적으로 배열을 사용하기도 한다.

Stack 예처럼 대다수의 제네릭 타입은 타입 매개변수에 아무런 제약을 두지 않는다. Stack<Object>, Stack<int[]>, Stack<List<String>>, Stack 등 어떤 참조 타입으로도 Stack을 만들 수 있다.

단, 기본 타입은 사용할 수 없다. Stack<int>나 Stack<double>을 만들려고 하면 컴파일 오류가 난다. => 박싱된 기본 타입으로 우회할 수 있다.

class DelayQueue<E extends Delayed> implements BlockingQueue<E> <br/>
타입 매개변수 목록인 <E extends Delayed>는 java.util.concurrent.Delayed의 하위 타입만 받는다는 뜻이다.

이렇게 하여 DelayQueue 자신과 DelayQueue를 사용하는 클라이언트는 DelayQueue의 원소에서 (형변환 없이) 곧바로 Delayed 클래스의 메서드를 호출할 수 있다.



