## 아이템 17 - 변경 가능성을 최소화하라

### Immutable Class (불변 클래스)

불변 클래스란, 객체가 생성된 이후 상태(내부 필드 값)가 절대 변경되지 않는 클래스를 말한다.

이러한 클래스는 객체의 불변성을 보장하며, 특히 멀티스레드 환경이나 캐싱과 같은 상황에서 안전하고 효율적으로 활용할 수 있다.

---

### 불변 클래스를 만드는 5가지 원칙

1.  `상태 변경 메서드 제공 금지`  
    set 메서드와 같은 변경 메서드를 제공하지 않는다.  
    객체가 생성된 이후 내부 상태는 절대로 변경되지 않아야 한다.
2.  `클래스 확장을 방지`  
    상속을 막기 위해 final 키워드를 사용한다.  
    상속이 가능하면 하위 클래스에서 내부 상태를 변경하는 메서드를 추가할 수 있다.
3.  `모든 필드를 final로 선언`  
    모든 필드는 반드시 final로 선언하여, 객체 생성 시 초기화 후 변경을 방지한다.
4.  `모든 필드를 private으로 선언`  
    외부에서 직접 접근을 막아야한다.  
    이를 통해 객체의 불변성을 유지할 수 있다.
5.  `가변 컴포넌트에 대한 접근 차단`  
    가변 객체를 필드로 포함할 경우, 이를 방어적 복사로 관리한다.  
    이를 통해 외부에서 내부 상태를 변경할 수 없도록 보호한다.

`예제 코드`

```java
@Getter
class AddressInfo {
    private String address;
}

@AllArgsConstructor
final class User {

    private final String phone;
    private final List<AddressInfo> addressInfoList;

    public List<String> getAddressList() {
        return addressInfoList.stream()
            .map(AddressInfo::getAddress)
            .collect(Collectors.toList());
    }
    
}
```

`설명`

User 클래스는 final로 선언되어 상속이 불가능하다.

모든 필드(phone, addressInfoList)는 final로 선언되어, 객체 생성 후 변경될 수 없다.

addressInfoList는 가변 객체(List)이므로, 내부적으로 처리하며 외부에는 직접 노출하지 않고 필요한 정보만 추출하여 반환한다.

BigInteger 예시로 본 Immutable Class

```java
BigInteger bigInteger = new BigInteger("10000");
System.out.println(bigInteger.add(new BigInteger("100"))); // 10100
System.out.println(bigInteger); // 10000
```

add 메서드는 새로운 객체를 반환하며, 기존 객체는 변경되지 않고 그대로 유지된다.

---

### Immutable Class의 조건

-   Thread Safe  
    여러 스레드에서 동시에 접근하더라도 상태가 변경되지 않으므로 안전하다.
-   Failure Atomicity  
    예외가 발생하더라도 객체의 상태는 항상 유효하게 유지된다.
-   값이 다르면 새로운 객체 생성  
    기존 객체를 수정하지 않고, 항상 새로운 객체를 생성하여 반환한다.

중간 단계 상태(객체 생성 도중 상태) 관리

```java
public static ImmutableClass of(String name) {
    return new ImmutableClass(name);
}
```

new 키워드를 사용한 직접 생성 대신 정적 팩토리 메서드를 사용한다.

이를 통해 객체 생성 과정에서 발생할 수 있는 중간 상태를 안전하게 관리할 수 있다.

---

### Immutable VO (Value Object)

`예제 코드`

```java
@Entity
@Setter
@Getter
public class Person {

    @Id
    private Long id;
    private String name;
    private float height;
    
}

@Getter
public class PersonVo {
    
    private final String name;
    private final float height;

    public PersonVo(String name, float height) {
        this.name = name;
        this.height = height;
    }

    public static PersonVo from(Person p) {
        return new PersonVo(p.getName(), p.getHeight());
    }
    
}
```

`설명`

-   Person Entity  
    데이터베이스와 매핑되며, 값이 변경 가능하다.  
    @Setter를 통해 값을 수정할 수 있도록 설계되어 있다.
-   PersonVo  
    불변 객체로 설계되어 값이 변경되지 않는다.  
    생성자를 통해서만 값을 설정하며, 모든 필드는 final로 선언되어 변경할 수 없다.