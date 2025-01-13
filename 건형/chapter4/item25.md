## 📖 톱레벨 클래스는 한 파일에 하나만 담으라

### 핵심 내용

- 소스 파일 하나에는 반드시 톱레벨 클래스 (혹은 톱레벨 인터페이스)를 하나만 담자.

- 이 규칙만 따른다면 컴파일러가 한 클래스에 대한 정의를 여러 개 만들어 내는 일은 사라진다.

- 소스 파일을 어떤 순서로 컴파일하든 바이너리 파일이나 프로그램의 동작이 달라지는 일은 결코 일어나지 않을 것이다.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

- 소스 파일 하나에 톱레벨 클래스를 여러 개 선언하더라도 자바 컴파일러는 불평하지 않는다.

- But, 아무런 득이 없을 뿐더러 심각한 위험을 감수해야 하는 행위 입니다.

- 이렇게 하면 한 클래스를 여러 가지로 정의할 수 있어며, 그중 어느 것을 사용할지는 어느 소스 파일을 먼저 컴파일하냐에 따라 달라지기 때문입니다.

```java
public class Main {
    public static void main(String[] args) {
                System.out.println(Utensil.NAME + Desert.NAME);
    }
}
```

**두 클래스가 한 파일(Utensil.java)에 정의되었다. - 따라하지 말 것!**
```java
class Utensil {
    static final String NAME = "pan";
}

class Desert {
    static final String NAME = "cake";
}
```

**두 클래스가 한 파일(Dessert.java)에 정의되었다. - 따라하지 말 것!**
```java
class Utensil {
    static final String NAME = "pot";
}

class Desert {
    static final String NAME = "pie";
}
```

- 컴파일러에 어느 소스 파일을 먼저 건내느냐에 따라 동작이 달라지므로 반드시 바로 잡아야 한다.

- 해결책은 단순히 톱레벨 클래스들을 서로 다른 소스 파일로 분리하면 그만이다.

- 굳이 여러 톱레벨 클래스를 한 파일에 담고 싶다면 정적 멤버 클래스를 사용하는 방법을 고민하라!

- 읽기 좋고, private으로 선언하면 접근 범위도 최소로 관리할 수 있기 때문입니다.

**톱레벨 클래스들을 정적 멤버 클래스로 바꿔본 모습**
```java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
    
    private static class Utensil {
        static final String NAME = "pan";
    }
    
    private static class Dessert {
        static final String NAME = "cake";
    }
}
```

## 추가 강의 내용 정리

## 🤔 생각 정리
- 궁금한 점, 논의하고 싶은 내용

