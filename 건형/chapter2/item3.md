## 📖 private 생성자나 열거 타입으로 싱글턴임을 보증하라
 
- 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다!

## 💡 주요 내용 정리 & 🛠️ 실습 코드

### 싱글턴을 만드는 방법

1. public static 멤버가 final 필드인 방식
```java
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
}
```

- 장점
- 해당 클래스가 싱글턴임이 API에 명백히 드러난다는 점
- 간결함


2. 정적 팩터리 메서드를 public static 멤버로 제공한다.
```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    
    public static Singleton getInstance() {
        return  INSTANCE;
    }
}
```

- 장점
- API를 바꾸지 않고도 싱글턴이 아니게 변경이 가능하는 점
- 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다는 점
    ```java
    public class Elvis<T> {
        private static final Elvis<Object> INSTANCE = new Elvis<>();
        
        private Elvis() {}
        
        public static <T> Elvis<T> getInstance() {
            return (Elvis<T>) INSTACNE;
        }
        
        public static void main(String[] args) {
            Elvis<String> elvis1 = Elvis.getInstance();
            Elvis<Integer> elvis2 = Elvis.getInstance();
        }
    }
    ```
- 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다는 점
    ```java
    public class MyClass {
        private String name;
    
        public MyClass(String name) {
            this.name = name;
        }
    
        public static MyClass create(String name) {
            return new MyClass(name);
        }
    }

    Supplier<MyClass> supplier = MyClass::create;
    
    MyClass myClass = supplier.get();
    ```
  
3. 열거 타입 방식의 싱글턴
```java
public enum Singleton {
    INSTANCE;
}
```
public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있고, 

심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽히 막아줍니다.

단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없습니다.


## 🤔 토론 및 질문
- 열거 타입 방식의 싱글턴을 사용해본 적이 있는지?
