## item27: 비 검사 경고를 제거하라

## **핵심 주제**
대부분의 비검사 경고는 쉽게 제거할 수 있다. 코드를 다음처럼 잘못 작성했다고 해보자.

```java
Set<Lark> exaltation = new HashSet();
```

javac 명령줄 인수에 -Xlint:uncheck 옵션을 추가

```java
Set<Lark> exaltation = new HashSet<>();
```

**할 수 있는 한 모든 비검사 경고를 제거하라.**

모두 제거한다면 그 코드는 타입 안정성이 보장된다.

**경고를 제거할 수는 없지만 타입 안전하다고 확신할 수 있다면 @Suppress Warning("unchecked") 애넡테이션을 달아 경고를 숨기자.**

안전하다고 검증된 비검사 경고를(숨기지 않고) 그대로 두면, 진짜 문제를 알리는 새로운 경고가 나와도 눈치채지 못할 수 있다. <br/>
제거하지 않은 수많은 거짓 경고 속에 새로운 경고가 파묻힐 것이기 때문이다.

@SuppressWarning 애너테이션은 개별 지역변수 선언부터 클래스 전체까지 어떤 선언에도 달 수 있다. 하지만 @SuppressWarnings 애너테이션은 항상 가능한 한 좁은 범위에 적용하자..!

한 줄이 넘는 메서드나 생성자에 달린 @SuppressWarning 애너테이션을 발견하면 지역 변수 선언 쪽으로 옮기자. <br/>
ArrayList에서 가져온 다음의 toArray 메서드를 예로 생각해보자.

```java
import java.util.Arrays;

public <T> T[] toArray(T[] a) {
    if (a.length < size)
        return (T[]) Arrays.copyOf(elements, size, a.getClass());
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

ArrayList 컴파일하면 이 메서드에서 다음 경고가 발생 (T[]) Arrays.copyOf(elements, size, a.getClass()) => unchecked cast return 에러 발생

애너테이션은 선언에만 달 수 있기 때문에 return 문에는 @SuppressWarning 다는게 불가능하다.

**지역변수를 추가해 @SuppressWarning의 범위를 좁힌다.**
```java
public <T> T[] toArray(T[] a) {
    if(a.length < size) {
        // 생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로
        // 올바른 형변환이다.
        @SuppressWarnings("unchecked") T[] result = (T[]) Arrays.copyOf(elments, size, a.getClass());
        return result;
    }
    System.arraycopy(elements, 0, a, 0, size);
    if(a.length > size) a[size] = null;
    return a;
}
```

@SuppressWarnings("unchecked") 애너 테이션을 사용할 때면 그 경고를 무시해도 안전한 이유를 주석으로 남겨야 한다.