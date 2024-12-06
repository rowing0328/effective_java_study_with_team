## **item5: 자원을 직접 명시하지 말고 의존 객체 주입을 사용해라.**

## **핵심 주제**

정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다. 
```java
public class SpellChecker {
    private static final Lexicon dictionary = ...;
    
    private SpellChecker() {}; // 객체
    public static boolean isValid(String word) { ... };
    public static List<String> suggestions(String typo) { ... };
}
```
왜냐면 테스트해야 할 부분이 최상단에 있지 않고 테스트하기 어려운 부분이랑 테스트해야하는 부분이 같이 코드가 똑같은 레벨에 있기에 문제가 됨.

싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다. 
```java
public class SpellChecker {
    private final Lexcion dictionary = ...;
    
    private SpellChecker(...) {}
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public boolean isValid(String word) {...};
    public List<String> suggestions(String typo) { ... }
}
```

**사용자 자원에 따라 동작이 달라지는 클래스에는 정적유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**

인스턴스 생성할 때 생성자에 필요한 자원을 넘겨주는 방식
```java
public class  SpellChecker {
    private final Lexicon dictionary;
    
    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```
- 의존 객체 주입 패턴 : 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있음.
- 해당 패턴의 쓸만한 변형으로, 생성자에 자원 팩터리를 넘겨주는 방식 
```java
public class Service {
    private final Supplier<Resource> resourceFactory;

    public Service(Supplier<Resource> resourceFactory) {
        this.resourceFactory = resourceFactory;
    }

    public void execute() {
        Resource resource = resourceFactory.get(); // 필요할 때마다 팩터리에서 가져옴
        resource.performTask();
    }
}

```


