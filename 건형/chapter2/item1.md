## 📖 생성자 대신 정적 팩터리 메서드를 고려하라
 
정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 장단섬을 이해하고 사용하는 것이 좋다.
그렇다고 하더라도 정적 팩터리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

### 정적 팩터리 매서드 장점

1. 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자 자체 만으로는 반환될 객체의 특성을 제대로 설명하지 못합니다.
반면 정적 팩터리는 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있습니다.

```java
public class Car {

    private final String name;

    private Car(String name) {
        this.name = name;
    }

    public static Car createFrom(String name) {
        return new Car(name);
    }
    
}
```

2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.

```java
public class Car {

    public static final Car car = null;
    
    private Car() {}

    public static Car getInstance() {
        if (car == null) {
            car = new Car();
        }
        return car;
    }

}
```

3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

```java
public interface Car {
    enum Model {
        TESLA,
        VOLVO,
        PORSCHE
    }

    void move();

    public static Car newInstance(Model model) {
        if (model.equals(Model.TESLA)) {
            return new Tesla();
        } else if (model.equals(Model.VOLVO)) {
            return new Volvo();
        } else if (model.equals(Model.PORSCHE)) {
            return new Porsche();
        }
        return null;
    }
}
```

4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

```java
public interface Car {
    enum Model {
        TESLA,
        VOLVO,
        PORSCHE
    }

    void move();

    public static Car newInstance(Model model) {
        if (model.equals(Model.TESLA)) {
            return new Tesla();
        } else if (model.equals(Model.VOLVO)) {
            return new Volvo();
        } else if (model.equals(Model.PORSCHE)) {
            return new Porsche();
        }
        return null;
    }
}
```

5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

```java
public interface Car {
    enum Model {
        TESLA,
        VOLVO,
        PORSCHE
    }

    void move();

    public static Car newInstance(Model model) {
        if (model.equals(Model.TESLA)) {
            return new Tesla();
        } else if (model.equals(Model.VOLVO)) {
            return new Volvo();
        } else if (model.equals(Model.PORSCHE)) {
            return new Porsche();
        }
        return null;
    }
}
```

### 정적 팩터리 메서드 단점

1. 상속하려먼 public 이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
2. 두 번째, 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

### 정적 팩터리 메서드 네이밍 규칙

- from : 매개 변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드 
    ```java
    Date d = Date.from(instant);  
    ```
- of : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
    ```java
    Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
    ```
- valueOf : from과 of의 더 자세한 버전
    ```java
    BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
    ```
- instance 혹은 getInstance : (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
    ```java
    StackWalker luke = StackWalker.getInstance(options);
    ```
- create 혹은 newInstance : instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장합니다.
    ```java
    Object newArray = Array.newInstance(classObject, arrayLen);
    ```
- getType : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 씁니다.
    ```java
    FileStore fs = Files.getFileStore(path);
    ```
- newType : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 씁니다.
    ```java
    BufferedReader br = Files.newBufferedReader(path);
    ```
- type : getType과 newType의 간결한 버전
    ```java
    List<Complaint> litany = Collections.list(legacyLitany);
    ```

## 🤔 토론 및 질문
- 궁금한 점, 논의하고 싶은 내용

- 네이밍 시 createFrom() 처럼 두가지 네이밍 규칙을 섞어서 사용해도 될지에 대한 고민이 존재했습니다.
- valueOf 같은 경우 from과 of의 더 자세한 버전이라고 하는데 of는 여러 매개변수를 받는 경우고 from은 매개 변수를 하나만 받는 경우라 valueOf는 매개변수를 몇개 사용할때 쓰라는건지 네이밍 규칙에 혼동이 생깁니다.
- 하나의 장점이 더 있다고 생각하게 되었습니다.
- 만약 구현 중 매개변수가 추가되게 되어서 인스턴스 생성 부분의 코드를 수정하여야 할 때에, 정적 팩토리 메서드를 사용하면
- 변경부를 한 곳에서 관리하게 되기 때문에 유지보수가 더 유용하지 않을까? 생각합니다!
