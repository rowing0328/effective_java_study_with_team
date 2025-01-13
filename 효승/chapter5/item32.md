## 아이템 32 - 제네릭과 가변 인수를 함께 쓸 때는 신중하라

### 가변 인수 (Variadic Arguments)

메서드가 불특정 다수의 인수를 받을 수 있도록 허용하는 기능이다.

메서드를 호출할 때 전달할 인수의 개수를 고정하지 않고, 원하는 개수만큼 전달할 수 있도록 설계된 문법이다.

사용 예시

1\. 가변 인수 선언

```java
public void printNumbers(int... numbers) {
    for (int number : numbers) {
        System.out.println(number);
    }
}
```

2\. 가변 인수 메서드 호출

```java
printNumbers(1, 2, 3);              // 개별 인수 전달
printNumbers(new int[]{4, 5, 6});   // 배열 전달
```

-   가변 인수 메서드를 호출할 때는 다음과 같이 다양한 형태로 전달할 수 있다.

### 힙 오염 (Heap Pollution)

자바에서 제네릭과 관련된 런타임 문제로, 제네릭 타입 안전성(type safety)이 깨지는 상황을 말한다.

이는 주로, 제네릭 배열 가변 인수, 그리고 비제네릭 코드와의 혼합에서 발생할 수 있다.

예제 코드

```java
class Printer {

    // 제네릭 가변 인수 메서드
    public <T> T[] toArray(T... args) {
        return args; // 가변 인수는 내부적으로 Object[] 배열로 처리됨
    }

    // 세 개의 값을 받아 배열로 반환
    public <T> T[] pick(T a, T b, T c) {
        T[] arr = toArray(a, b, c); // 가변 인수 메서드 호출
        return arr;
    }
    
}

public class Main {
    public static void main(String[] args) {
        Printer p = new Printer();

        // pick 메서드 호출: String 타입으로 기대
        String[] s = p.pick("1", "2", "3"); // 런타임 오류 가능성
    }
}
```

### 가변 인수와 제네릭의 동작 방식의 문제점

toArray(T... args)는 가변 인수를 받아 배열로 반환하는 메서드이다.

가변 인수는 내부적으로 Object\[\] 배열로 처리되며, 컴파일 시 제네릭 타입 정보는 사라지게 된다.

문제는 이렇게 반환된 배열이 실제로는 Object \[\] 타입인데,

호출하는 쪽에서는 이를 T \[\](예를 들어 String \[\]) 타입으로 간주하고 사용한다는 점이다.

이 과정에서 타입이 맞지 않으면,

런타임에 ClassCastException이 발생할 가능성이 있다.

컴파일 단계에서는 아무 문제가 없어 보이지만,

실행 중에 갑자기 오류가 발생할 수 있기 때문에 주의가 필요하다.

### 불확실한 타입 안정성

T \[\] arr = toArray(a, b, c);에서, toArray 메서드는 실제로 Object\[\] 배열을 반환한다.

문제는 이후에 String\[\] s = p.pick("1", "2", "3");처럼 배열을 사용하려고 하면,

Object \[\]를

이로 인해 런타임에 ClassCastException이 발생할 가능성이 있다.

pick 메서드는 반환 타입을 T \[\]로 지정했지만,

내부적으로 반환되는 배열은 실제로 Object \[\] 타입이다.

호출자가 이를 타입 안전한 T \[\]로 착각하는 것이 문제의 핵심이다.

### 힙 오염이 발생하는 이유

-   제네릭 타입 소거 (Type Erasure)  
    자바의 제네릭은 컴파일 타임에만 타입 정보를 검사하고, 런타임에는 타입 정보가 소거된다.  
    따라서, 가변 인수로 전달된 T... args는 내부적으로 Object \[\]로 처리되며, 제네릭 타입 정보(T)는 런타임에 유지되지 않는다.
-   가변 인수와 배열의 결합  
    자바의 가변 인수는 내부적으로 배열로 구현된다.  
    가변 인수를 사용할 때는 제네릭 타입(T)이 아닌 Object \[\] 배열이 생성된다.  
    이 배열은 호출한 코드에서는 T \[\] 배열로 처리한다고 믿기 때문에, 런타임에 타입 불일치 문제가 발생한다.

### 힙 오염을 방지하기 위한 해결 방법

1\. 제네릭 배열 대신 List 사용

```java
import java.util.Arrays;
import java.util.List;

class Printer {
    
    public <T> List<T> toArray(T... args) {
        return Arrays.asList(args); // 가변 인수를 List로 반환
    }

    public <T> List<T> pick(T a, T b, T c) {
        return toArray(a, b, c); // 안전한 List 반환
    }

}

public class Main {
    public static void main(String[] args) {
        Printer p = new Printer();
        List<String> s = p.pick("1", "2", "3"); // 정상 동작
        System.out.println(s); // 출력: [1, 2, 3]
    }
}
```

-   List를 사용하면 제네릭 타입 정보를 런타임에 유지할 수 있어 타입 안정성을 보장할 수 있다.

2\. @SafeVarargs 어노테이션

```java
class Printer {

    @SafeVarargs
    public final <T> T[] toArray(T... args) {
        return args; // 안전한 작업만 수행
    }

    public <T> T[] pick(T a, T b, T c) {
        return toArray(a, b, c);
    }

}

public class Main {
    public static void main(String[] args) {
        Printer p = new Printer();
        String[] s = p.pick("1", "2", "3"); // @SafeVarargs로 경고 억제
        System.out.println(Arrays.toString(s)); // 출력: [1, 2, 3]
    }
}
```

-   @SafeVarargs는 호출자 측 경고를 억제할 뿐, 메서드 내부의 타입 안정성을 보장하지 않는다.
-   안전한 작업만 수행해야 하며, 가변 인수 배열을 외부로 노출하거나 수정하는 작업은 피해야 한다.
