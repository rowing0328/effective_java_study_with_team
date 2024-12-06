## **item7: 다 쓴 객체 참조를 해제하라**

## **핵심 주제**

```java
import java.util.Arrays;
import java.util.EmptyStackException;

public class Stack {
    private Object[] elements;
    private int size;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) throw new EmptyStackException();
        return elements[--size];
    }

    private void ensureCapacity() {
        if (elements.length == size) elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

스택에 있는 배열을 줄이면 더 이상 사용하지 않으면 메모리를 가비지 켈럭터가 수거 할 줄 알았지만 수거해가지 않는다. 

다 쓴 참조(obsolete reference)를 여전히 가지고 있기 때문이다. 

객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체 뿐아니라 참조하는 모든 객체까지 회수해가지 못한다.

** 제대로 구현한 pop 메서드**
```
public Object pop() {
    if (size == 0) throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null;
    return result;
}
```
**객체를 참조하고 null 처리하는 일은 예외적인 경우야 한다.**

가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope)밖으로 밀어내는 것이다.

```java
public void processJobInfos() {
    List<String> jobInfos = getJobInfos(); // 지역 변수 생성
    for (String job : jobInfos) {
        System.out.println(job);
    }
    // 유효 범위를 벗어나면 jobInfos는 더 이상 접근할 수 없음
    // GC가 필요 시 jobInfos 객체를 회수
}
```
