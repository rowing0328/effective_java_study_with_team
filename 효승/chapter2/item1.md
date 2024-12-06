**※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.**

## 아이템 1 - 생성자 대신 정적 팩터리 메서드를 고려하라

```java
// 생성자를 사용하는 경우
new Member("hyoseung", MemberType.ADMIN); // true가 뭘 의미하는지 모호하다.

// 정적 팩터리 메서드를 사용하는 경우
User user = User.createAdminUser(); // 관리자 생성임을 바로 알 수 있다.
```

-   `이름을 붙일 수 있다.`  
    정적 팩터리 메서드는 이름을 통해 의도를 명확히 드러낼 수 있다.  
    반면, 생성자는 이름을 붙일 수 없어 "이게 뭐 하는 생성자인지" 헷갈릴 수 있다.

-   `판단 기준이 명확하다.`  
    생성자에서 boolean 같은 값으로 구분하면 의미가 불분명하고 실수할 가능성이 높다.  
    정적 팩터리 메서드는 명확한 이름으로 로직을 전달하니 더 직관적이다.

-   `사용성 향상 및 오류 방지`   
    이름 덕분에 코드가 더 읽기 쉽고 유지보수가 편하다.  
    잘못된 값을 전달할 위험도 줄어든다.

<br>

```java
// Singleton pattern - Single Object
class ConnectionManager {

    // 생성자를 private으로 선언해 외부에서 인스턴스를 생성하지 못하게 한다.
    private ConnectionManager() {
    }

    // 내부클래스에서 단 한번만 인스턴스를 생성한다.
    private static class ConnectionManagerHolder { 
        private static final ConnectionManager instance = new ConnectionManager();
    }

    // 정적 팩토리 메서드를 통해 인스턴스를 제공한다.
    public static ConnectionManager getInstance() { 
        return ConnectionManagerHolder.instance;
    }

}

// Flyweight pattern = Collection Object
class Icon {

    private final String filePath;

    // 캐싱을 위한 저장소
    private static final Map<String, Icon> cache = new HashMap<>();

    private Icon(String filePath) {
        this.filePath = filePath;
    }

    // 정적 팩터리 메서드를 통해 인스턴스를 제공한다.
    public static Icon getIcon(String filePath) {
        return cache.computeIfAbsent(filePath, Icon::new); // 이미 존재하면 반환, 없으면 생성
    }

}
```

-   `호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.`  
    객체 생성 과정을 개발자가 직접 제어할 수 있다.  
    동일한 객체를 반환해 메모리 사용을 효율화할 수 있다.  
    싱글톤 패턴을 통해 애플리케이션 내에서 단일 인스턴스를 보장할 수 있다.  
    Flyweight 패턴처럼 중복 생성을 방지하고 객체를 재사용할 수 있다.

<br>

```java
// 인터페이스 정의
interface MemberRepository {

    void save(String memberName);

}

// 구현체 A
class InMemoryMemberRepository implements MemberRepository {

    public void save(String memberName) {
        System.out.println(memberName + " saved in memory.");
    }

}

// 구현체 B
class DatabaseMemberRepository implements MemberRepository {

    public void save(String memberName) {
        System.out.println(memberName + " saved in database.");
    }

}

// 정적 팩터리 메서드를 제공하는 클래스
class MemberRepositoryFactory {

    public static MemberRepository getRepository(String type) {
        if ("memory".equalsIgnoreCase(type)) {
            return new InMemoryMemberRepository();
        } else if ("database".equalsIgnoreCase(type)) {
            return new DatabaseMemberRepository();
        }
        throw new IllegalArgumentException("Unknown repository type: " + type);
    }

}

public class Main {

    public static void main(String[] args) {
        // 메모리 기반 구현체 반환
        MemberRepository memoryRepo = MemberRepositoryFactory.getRepository("memory");
        memoryRepo.save("Alice");

        // 데이터베이스 기반 구현체 반환
        MemberRepository databaseRepo = MemberRepositoryFactory.getRepository("database");
        databaseRepo.save("Bob");
    }

}
```

-   `반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.`  
    정적 팩터리 메서드는 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.  
    인터페이스나 상위 타입을 반환하여 클라이언트는 구현 세부 사항에 의존하지 않아도 된다.  
    구현체를 숨길 수 있어, 반환 객체를 바꿔도 클라이언트 코드는 수정할 필요가 없다.  
    다형성을 활용하면 새로운 구현체 추가 시 기존 코드를 수정하지 않아도 된다.  
    스프링의 의존성 주입(DI)처럼 객체 생성과 사용을 깔끔하게 분리할 수 있다.

<br>

```java
@Bean
public PasswordEncoder passwordEncoder() {
    // 실제 구현체는 런타임 시점에 결정됨
    return new BCryptPasswordEncoder();
}
```

-   `정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.`  
