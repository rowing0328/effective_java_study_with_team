## **📖item12: clone 재정의는 주의해서 진행하라**

## **💡핵심 주제**
Cloneable 복제해도 되는 클래스임을 명시 하는 용도의 믹스인 인터페이스지만, 아쉽게도 의도한 목적을 제대로 이루지 못함<br/>
clone 메서드가 선언된 곳이 Cloneable x Object 그마저도 protected <br/>
Cloneable 구현하는 것으로 외부 객체에서 clone 메서드 호출 ❌ , 리플렉션을 사용하면 가능하지만, 100% 성공 보장 ❌ <br/>
해당 객체 접근 허용된 clone 메서드 제공 보장 ❌ <br/>

메서드 하나 없는 Cloneable 인터페이스 How work? <br/>
이 인터페이스는 놀랍게도 Object의 protected 메서드인 clone 동작 방식 결정 <br/>
Cloneable 구현한 클래스의 인스턴스 clone 호출하면 그 객체 필드들을 하나하나 복사한 객체를 반환 , but cloneable 구현 ❌ CloneNotSupportedException <br/>
인터페이스를 상당히 이례적으로 사용한 예이니 따라 x. 인터페이스를 구현한다는 것은 일반적으로 해당 클래스가 제공한다고 선언하는 행위다. Cloneable 경우에는 상위 클래스에 정의된 protected 메서드의 동작 방식으로 변경 <br/>

복사본을 생성해 반환한다. '복사'의 정확한 뜻은 그 객체를 구현한 클래스에 따라 다를 수 있음. <br/>
1. x.clone() != x also x.clone().getClass() == x.getClass() 참이다. <br/>
2. x.clone().equals(x) <br/>
   - 관례상, 이 메서드가 반환하는 객체는 super.clone 호출해 얻어야한다. 이 클래스와 (Object 제외) 모든 상위 클래스가 이 관례를 따르면 다음 식 참이다. <br/>
3. x.clone().class() = x.getClass(); <br/>

- 강제성이 없다는 점만 빼면 생성자 연쇄 비슷 즉, clone 메서드가 super.clone 아닌, 생성자를 호출해 얻은 인스턴스 반환 가능 컴파일 오류 ❌ <br/>
- but 이 클래스의 하위 클래스에서 super.clone 호출 시, 잘못된 객체 생성 ➡️ finally 하위 클래스 clone 메서드 동작 ❌ <br/>
- clone 재정의한 클래스가 final 이면, 하위 클래스 없으니 관례 무시 🙆 but final 클래스 clone 메서드가 super.clone 호출 ❌ Cloneable 구현 이유 없음

**clone 메서드 가진 상위 클래스 상속 Cloneable 구현**
1. super.clone 호출
2. 얻은 객체 완벽한 복제본
3. 클래스 정의된 모든 필드 원본 필드와 같은 값
4. 모든 필드 기본 , 불변 객체 참조 객체는 완벽히 우리가 원하는 상태 손볼 곳 없음
**쓸데없는 복사를 지양한다는 관점에서 보면 굳이 불변 클래스 clone 메서드 제공 ❌ 권장**

**가변 상태를 참조하지 않는 클래스용 clone 메서드**
```java
@Override public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```

clone 메서드를 이런식으로 만드는 순간 재앙이 찾아온다.

**Stack 클래스를 예로 설명**

```java
public class Stack {
    private object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if(size == 0) throw new EmptyStackException();
        Object result = elements[--size];
        element[size] = null;
        return result;
    }
    
    // 원소를 위한 공간을 적어도 하나 이상 확보
    private void ensureCapacity() {
        if (elements.length == size) elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

**클래스를 복제할 수 있도록 만들어보자. clone 메서드가 단순히 super.clone 결과 그대로 반환 how?**

1. 반환된 Stack 인스턴스 , size 올바른 값 가짐 burt elements 필드는 원본 Stack 인스턴스와 똑같은 배열 참조.<br/>
2. **원본이나 복제본 중 하나를 수정하면 다른 하나도 수정되어 불변식 해제** <= 치명적인 버그가 될 수 있음. <br/>
3. Stack 클래스 하나뿐인 생성자 호출한다면 이러한 상황은 일어나지 않음. <br/>
4. clone 메서드는 사실상 생성자와 같은 효과 즉, 🔥 **clone은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야함** 🔥

stack clone 메서드 정상 동작 스택 내부 정보 복사 => 가장 쉬운 방법 elements 배열의 clone을 재귀적으로 호출

**가변 상태를 참조하는 클래스용 clone 메서드**
```
@Override
public Stack clone() {
    try{
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch(CloneNotSupportedException e) {
        throw new AssertionsError();
    }
}
위에 코드가 왜 정상적으로 동작?
```
**배열인 경우 clone 메서드가 유일하게 정상적으로 작동 why?** <br/>
Java에서 배열의 clone() 메서드가 유일하게 “정상적으로” 동작하는 이유는 배열이 기본적으로 깊은 복사처럼 동작하는 특별한 구조를 가지고 있기 때문입니다.<br/>
1. 배열의 clone() 메서드는 기본적으로 **얕은 복사(Shallow Copy)**를 수행합니다.
2. 그러나 배열이 가지고 있는 요소들은 메모리 상에서 독립적인 복사본을 가지므로, 1차원 배열에서는 결과적으로 올바르게 동작합니다.


**Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라'는 일반 용법과 충돌** <br/>
그래서 복제할 수 있는 클래스를 만들기 위해 일부 필드 final 한정자 제거해야 할 수 있음.
```java
public class HashTable implements Cloneable {
    private Entry[] buckets = ...;
    
    private static class Entry {
        final Object key;
        Object value;
        Entry next;
        
        Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value =value;
            this.next = next;
        }
    }
    ... 코드 생략
}
```

** 잘못된 clone메서드 - 가변 상태 공유**

```java
import java.util.Hashtable;

@Override
public Hashtable clone() {
    try {
        HashTable result = (HashTable) super.clone();
        result.buckets = buckets.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```

복제본 자신만의 버킷 배열 갖지만, 원본과 똑같은 연결리스트 참조하여 예기치 않게 동작

**복잡한 가별 상태를 갖는 클래스용 재귀적 clone 메서드**
```java
public class HashTable implements Cloneable {
    private Entry[] buckets = ...;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value =value;
            this.next = next;
        }
    
        Entry deepCopy() {
            return new Entry(key, value, next == null? null : next.deepCopy());
        }
    }

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++) {
                if(bucket[i] != null) result.buckets[i] = buckets[i].deepCopy();
            }
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}

```

deepCopy 메서드 자신이 가르키는 연결 리스트 전체를 복사하기 위해 재귀적으로 호출한다. <br/>
연결리스트 복제하는 방법 좋지 않음 <br/>
재귀 호출 때문에 리스트의 원소 수만큼 스택 프레임 소비, 리스트가 길면 스택 오버플로우 가능성 있음 <br/>
deepCopy를 재귀 호출 대신 반복자 써서 순회하는 방향으로 수정하면 해결

**엔트리 자신이 가리키는 연결리스트를 반복적으로 복사**

```java
import java.util.Map;

Entry deepCopy() {
    Entry result = new Entry(key, value, next);
    for (Entry p = result; p.next != null; p = p.next) {
        p.next = new Entry(p.next.key, p.next.value, p.next.next);
    }
    return result;
}
```

public인 clone메서드에서는 throws 절을 없애야 한다. 검사 예외를 던지지 않아야 메서드 사용하기 편하기 때문이다.

**상속용 클래스는 Clonneable 구현하면 안된다.**<br/>
1. 상속용 클래스로 하려면  Object 방식 모방 => 제대로 작동하는 clone 메서드 구현 protected 두고 CloneNotSupportedException 던질 수 있다고 선언
2. clone 동작하지 않게 구현 하위 클래스 재정의 못하게 만드는 방법
```java
@Override
protected final Object clone throw CloneNotSupportedException {
    throw new CloneNotSupportedException();
}
```

Cloneable을 구현한 스레드 안전 클래스 작성 할 때, clone 메서드 역시 동기화해줘야함. <br/>
super.clone 호출 외에 다른 할 일이 없더라도 clone 재정의 및 동기화 필수 <br/>
한마디로 말하면, Cloneable 구현하는 모든 클래스 clone 재정의 이때, 접근 제한자는 public , 반환 타입은 클래스 자신으로 변경 <br/>

Clonneable 이미 구현한 클래스 확장한다면 clone 잘 작동되도록 구현아니라면 **복사 생성자와 복사 팩터리는 더 나은 객체 복사 방식을 제공**

**복사 생성자**
```
public Yum(Yum yum) {...};
```

**복사 팩터리**
```java
public static  Yum newInstance(Yum yum) { ... };
```

Cloneable/ clone 방식보다 나은 점
1. 언어 모순적이고 위험천만한 객체 생성 메커니즘 사용 ❌
2. 엉상하게 된 규약에 기대지 않음
3. 정상적인 final 필드 용법과 충돌하지 않음
4. 불필요한 검사 예외 던지지 않음, 형변환도 필요 ❌
5. 인터페이스 타입의 인스턴스를 인수로 받을 수 있음.
6. 원본 타입에 얽매이지 않고 복제 타입을 복제 할 수 있음.