# Effective_Java_Study


## 📖 변경 가능성을 최소화하라

### 핵심 정리

클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다!

## 💡 주요 내용 정리 & 🛠️ 실습 코드

불변 클래스 : 인스턴스의 내부 갑슬 수정할 수 없는 클래스
ex) String, 기본 타입의 박싱된 클래스들, BigInteger, BigDecimal

불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전하다.


### 클래스를 불변으로 만들기 위한 다섯 가지 규칙

1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.

2. 클래스를 확장할 수 없도록 한다.

3. 모든 필드를 final로 선언한다.

4. 모든 필드를 private으로 선언한다.

5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.


### 불변 객체는 단순하다

불변 객체는 생성된 시점의 상태를 파괴될 때까지 그대로 간직한다.

모든 생성자가 클래스 불변식을 보장한다면 그 클래스를 사용하는 프로그래머가 다른 노력을 들이지 않더라도 영원히 불변으로 남는다.

반면 가변 객체는 임의의 복잡한 상태에 놓일 수 있다.

변경자 메서드가 일으키는 상태 전이를 정밀하게 문서로 남겨놓지 않은 가변 클래스는 믿고 사용하기 어려울 수도 있다.


### 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다.

여러 스레드가 동시에 사용해도 절대 훼손되지 않는다.

클래스를 스레드 안전하게 만드는 가장 쉬운 방법이기도 하다.

불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 불변 객체는 안심하고 공유할 수 있다.


### 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.

### 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.

값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 그 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월합니다.

### 불변 객체는 그 자체로 실패 원자성을 제공한다.

### 불변 글래스에도 단점은 있다. 값이 다르면 반드시 독립된 객체로 만들어야 한다.

### 클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다.

불변 클래스는 장점이 많으며, 단점이라곤 특정 상황에서의 잠재적 성능 저하뿐이다.

### 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자

객체가 가질 수 있는 상태의 수를 줄이면 그 객체를 예측하기 쉬워지고 오류가 생길 가능성이 줄어든다.

그러니 꼭 변경해야 할 필드를 뺀 나머지 모두를 final로 선언하자.

### 다른 합당한 이유가 없다면 모든 필드는 private final 이어야 한다.

### 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.

확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안 된다.

객체를 재활용할 목적으로 상태를 다시 초기화하는 메서드도 안 된다.

복잡성만 커지고 성능 이점은 거의 없다!


### 불변 클래스를 만드는 또 다른 설계 방법 - 정적 팩토리 

```java
public class Complex {
    private final double re;
    private final double im;
    
    private Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }
    
    public static Complex valueOf(double re, double im) {
        return new Complex(re, im);
    }
    
    // ... 나머지 코드 생략
}
```
- 정적 팩터리 방식은 다수의 구현 클래스를 활용한 유연성을 제공하고, 이에 더해 다음 릴리스에서 객체 캐싱 기능을 추가해 성능을 끌어올릴 수도 있다.


## 추가 강의 내용 정리

### 핵심 내용

변경 가능성을 최소화 하라!

### Immutable class

1. 상태 변경 method (ex. Set method) 제공하지 않는다
2. Class 확장하지 않도록 한다. (ex. Final)
3. 모든 field를 final로 선언한다.
4. 모든 field를 private로 선언한다.
5. 자신을 제외하고는 아무도 가변 컴포넌트에 접근할 수 없도록 한다.
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
        // 내부에 가변 컴포넌트인 주소 정보를 숨겼다.
        return addressInfoList.stream()
                .map(AddressInfo::getAddress).toList();
    }
}
```

### BigInteger (Immutable class example)

```java
import java.math.BigInteger;

public static void main(String[] args) {
    BigInteger bigInteger = new BigInteger("10000");
    System.out.println(bigInteger.add(new BigInteger("100"))); // 10100
    System.out.println(bigInteger); // 10000
}

```

Immutable class의 조건

1. Thread safe
2. failure atomicity - 예외가 발생 후에도 유효한 상태
3. 값이 다르면 무조건 독립적인 객체로 생성되어야 함

### 중간 단계 (객체가 완성 중인 상태)를 극복하기 위한 방법

Static factory method를 통해 new instance를 생성해 response
Ex) StringBuilder.

### Immutable Vo

Person Entity
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
```

PersonVo (Immutable)
```java
@Getter
public class PersonVo {
    private final String name;
    private final float height;
    
    private PersonVo(String name, float height) {
        this.name = name;
        this.height = height;
    }
    
    public static final PersonVo from(Person p) {
        return new PersonVo(p.getName(), p.getHeight());
    }
}
```
값이 변하지 않는 객체라면 다음과 같이 불변성을 강제하는 것 또한 방법이다.

### 주의 사항

- 습관적으로 Setter를 만들지 말자 (Setter가 불필요할 수도 있다.)
- Class는 꼭 필요한 경우가 아니면 불변 이어야 한다.
- 특히 단순한 Value Object는 더 그러하다.
- 모든 클래스가 불변일 수 없지만, 변경할 수 있는 부분을 최대한 줄여보자
