※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.

## 아이템 13 - clone 재정의는 주의해서 사용하라

clone() 메서드는 객체의 복사본을 반환하는 기능을 한다.

하지만 복사의 정확한 의미는 클래스마다 다를 수 있으며, 이를 잘못 구현하거나 사용할 경우 예상치 못한 문제를 초래할 위험이 있다.

---

### 배열 복사의 두가지 방법

```java
// Shallow Copy (얕은 복사)
int[] a = {1,2,3,4};
int[] b = a;
```

b는 a와 동일한 참조값을 가지므로, 같은 배열을 가리킨다.

따라서 배열의 내용이 변경되면 a와 b 모두 영향을 받는다.

```java
// Deep copy (깊은 복사)
b = a.clone();
```

clone() 메서드를 사용하면 새로운 배결 객체가 생성된다.

두 배열의 요소는 동일하지만, a와 b는 서로 다른 참조값을 가진다.

즉, 한 배열의 내용이 변경되어도 다른 배열에는 영향을 미치지 않는다.

---

### clone() 사용 시 주의사항

```java
Laptop[] a = {new Laptop("그램 16인치", "삼성")};
Laptop[] b = a.clone();
b[0].setCompany("LG");

System.out.println(a[0] == b[0]); // true
```

clone() 메서드는 얕은 복사(Shallow Copy)를 수행한다.

이로 인해 b는 a와 별도의 배열 객체이지만, 배열 내부 요소들은 같은 객체를 참조한다.

따라서, a\[0\]과 b\[0\]는 동일한 객체를 가리키며, 한쪽에서 객체의 내용을 변경하면 다른 쪽에도 영향을 미친다.

---

### 왜 이런 현상이 발생하는가?

a.clone()은 배열의 객체 참조만 복사하기 때문이다.

이로 인해 객체의 내용이 아닌 참조값이 복사되며, 복사된 배열과 원본 배열은 동일한 객체를 가리키게 된다.

---

### Why not use clone() ?

```java
class A implements Cloneable {
    public A clone() throws CloneNotSupportedException {
        return (A) super.clone();
    }
}

class B extends A {
    public B clone() throws CloneNotSupportedException {
        return (B) super.clone(); // A의 clone이 실행됨
    }
}
```

clone() 메서드가 객체의 복사본을 생성하지만, 클래스에 따라 동작이 다르다.

상속 구족에서 clone()을 사용할 경우, 하위 클래스 객체가 제대로 복사되지 않는 문제가 발생할 수 있다.

이는 상위 클래스의 clone() 메서드가 호출되기 때문에, 하위 클래스의 추가 필드가 누락되거나 불완전한 복사로 이어질 수 있다.

---

### clone() 대신 명확하고 안전한 복사 방법

```java
// Copy Constructor (명확한 복사)
public Yum(Yum yum) {
    this.field1 = yum.field1;
    this.field2 = yum.field2;
}

// Copy Factory Method (유연하고 직관적인 복사)
public static Yum newInstance(Yum yum) {
    Yum copy = new Yum();
    copy.field1 = yum.field1;
    copy.field2 = yum.field2;
    return copy;
}
```

Primitive 타입 배열이 아닌 경우, clone()은 얕은 복사로 인해 참조 공유 문제가 발생할 수 있으므로 사용하지 말자.

Cloneable 인터페이스는 구현 방식에 따라 불완전한 복사가 이루어질 수 있어 확장에 주의해야 한다.