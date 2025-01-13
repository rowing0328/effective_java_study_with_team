## 아이템 40 - Override 에노테이션을 일관되게 사용하라

### @Override Annotation의 효능

-   컴파일 타임 오류 방지  
    @Override를 사용했을 때, 실제로 Override 되지 않은 메서드에 대해 컴파일 시점에 오류를 발생시킨다.  
    이로써 실수를 줄이고 재정의 여부를 명확히 확인할 수 있다.
-   프로그래머에게 명확성 제공  
    @Override를 통해 해당 메서드가 재정의되었다는 사실을 명시적으로 나타낸다.  
    이는 협업 중인 다른 개발자에게도 중요한 정보를 제공한다.

### Annotation의 일관된 사용

-   권장 사항  
    모든 재정의 메서드에 @Override Annotation을 명시적으로 추가한다.  
    특히, 인터페이스 메서드를 재정의하는 경우에도 @Override를 사용하는 것이 좋다.
-   예외 사항  
    추상 메서드를 재정의할 때는 @Override를 생략해도 무방하다.  
    그러나, 코드의 일관성을 위해 이 경우에도 사용하는 것이 권장된다.

### @Override Annotation 사용 예시

```java
// 올바른 사용 예시
public class Animal {

    public void sound() {
        System.out.println("Animal makes a sound");
    }
    
}

public class Dog extends Animal {

    @Override
    public void sound() { // 재정의 명시
        System.out.println("Dog barks");
    }
    
}

// 잘못된 사용 예시
public class Dog extends Animal {
    
    public void soud() { // 1. 오타로 인한 새로운 메서드 생성
        System.out.println("Dog barks");
    }
    
} // 2. `@Override`가 없으므로 컴파일러가 오류를 감지하지 못함.
```
