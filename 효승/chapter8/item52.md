## 아이템 52 - 다중 정의는 신중히 사용하라

### Overloading의 위험성

메서드 오버로딩(Overloading)은 동일한 이름의 메서드를 여러 개 정의하는 기능이다.

하지만 매개변수가 같은 오버로딩은 코드의 혼란과 버그를 유발할 수 있다.

예를 들어, 아래와 같은 코드가 있다고 가정해보자.

```java
public List<E> sort(ArrayList<E> arrayList) {
    // 알고리즘 1번
    return arrayList;
}

public List<E> sort(LinkedList<E> arrayList) {
    // 알고리즘 2번
    return arrayList;
}
```

-   메서드 이름은 같지만 매개변수 타입만 다르다.
-   호출자가 매개변수 타입을 혼동하거나, 의도와 다른 메서드가 호출될 위험이 있다.

해결 방법은 아래와 같다.

1\. 매개 변수 타입이 다른 경우에도 메서드 이름을 명확히 구분한다.

```
public List<E> sortFromArrayList(ArrayList<E> arrayList) {
    // 알고리즘 1번
    return arrayList;
}

public List<E> sortFromLinkedList(LinkedList<E> arrayList) {
    // 알고리즘 2번
    return arrayList;
}
```

2\. 생성자와 같이 형변환이 발생할 수 있는 경우에도 오버로딩을 피하는 것이 좋다.

```java
public class OverloadingExample {

    public OverloadingExample(int value) {
        System.out.println("int 생성자 호출");
    }

    public OverloadingExample(double value) {
        System.out.println("double 생성자 호출");
    }

    public static void main(String[] args) {
        OverloadingExample example1 = new OverloadingExample(10);        // int 생성자 호출
        OverloadingExample example2 = new OverloadingExample(10.0);      // double 생성자 호출

        OverloadingExample example3 = new OverloadingExample(10L);       // long -> double로 자동 형변환, double 생성자 호출
    }
    
}
```
