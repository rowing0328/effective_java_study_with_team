# Effective_Java_Study


## 📖 클래스와 멤버의 접근 권한을 최소화하라

### 핵심 정리

프로그램 요소의 접근성은 가능한 한 최소한으로 하라!!!

꼭 필요한 것만 골라 최소한의 public API를 설계하자.

그 외에는 클래스, 인터페이스, 멤버가 의도치 않게 API로 공개 되는 일이 없도록 해야 한다.

public 클래스는 상수용 public static final 필드 외에는 어떠한 public 필드로 가져서는 안 된다.

public static final 필드가 참조하는 객체가 불변인지 확인하라.

## 💡 주요 내용 정리 & 🛠️ 실습 코드

어설프게 설계된 컴포넌트와 잘 설계된 컴포넌트의 가장 큰 차이는 바로 클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 얼마나 잘 숨겼느냐다.

### 정보 은닉의 장점

1. 시스템 개발 속도를 높인다. 
   - 여러 컴포는트를 병렬로 개발할 수 있기 때문이다.
2. 시스템 관리 비용을 낮춘다.
   - 각 컴포넌트를 더 빨리 파악하여 디버깅할 수 있고, 다른 컴포넌트로 교체하는 부담도 적기 때문이다.
3. 정보 은닉 자체가 성능을 높여주지는 않지만, 성능 최적화에 도움을 준다.
4. 소프트웨어 재사용성을 높인다.
5. 큰 시스템을 제작하는 난이도를 낮춰준다.
   - 시스템 전체가 아직 완성되지 않은 상태에서도 개별 컴포넌트의 동작을 검증할 수 있기 때문이다.

### 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.

톱레벨 클래스나 인터페이스를 public으로 선언하면 공개 API가 되며, package-private으로 선언하면 해당 패키지 안에서만 이용할 수 있다.

패키지 외부에서 쓸 이유가 없다면 package-private으로 선언하자.

그러면 이들은 API가 아닌 내부 구현이 되어 언제든 수정할 수 있다.

즉, 클라이언트에 아무런 피해 없이 다음 릴리스에서 수정, 교체, 제거할 수 있다.

반면, public으로 선언한다면 API가 되므로 하위 호환을 위해 영원히 관리해줘야 한다!

**public일 필요가 없는 클래스의 접근 수준을 package-private 톱레벨 클래스로 좁히는 것이 중요하다!**

```text
접근 제한자

private : 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다.

package-private (default) : 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. 접근 제한자를 명시하지 않았을 때 적용되는 패키지 접근 수준이다.
                            ( 단, 인터페이스의 멤버는 기본적으로 public 이 적용된다. )
                            
protected : package-private의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다.

public : 모든 곳에서 접근할 수 있다.

```

### 단지 코드를 테스트하려는 목적으로 클래스, 인터페이스, 멤버의 접근 범위를 넓히고자 할 떄는, package-private 까지만 풀어주자!

테스트만을 위해 클래스, 인터페이스, 멤버를 공개 API로 만들어서는 안 된다!!

### public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.

필드가 가변 객체를 참조하거나, final이 아닌 인스턴스 필드를 public으로 선언하면 그 필드에 담을 수 있는 값을 제한할 힘을 잃게 된다.

그러면, 그 필드와 관련된 모든 것은 불변식을 보장할 수 없게 된다!

여기에 더해, 필드가 수정될 때 (락 획득 같은) 다른 작업을 할 수 없게 되므로 **public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다!**


### 해당 클래스가 표현하는 추상 개념을 완성하는 데 꼭 필요한 상수라면 public static final 필드로 공개해도 좋다.

### 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안된다.

1. 코드의 public 배열을 private로 만들고 public 불변 리스트를 추가하자.
2. 코드의 public 배열을 private로 만들고 그 복사본을 반환하는 public 메서드를 추가하자. (방어적 복사)



## 추가 강의 내용 정리

### 핵심 한 줄 정리

클래스와 멤버의 접근 권한을 최소화 하라.

### Public class의 instance field

1. Public으로 열 경우 Thread safe 하지 못하다. (Lock 류의 작업을 걸 수 없음)
2. 꼭 필요한 상수라면 예외적으로 public static final로 공개 할 수 있다.
3. [주의사항] public static final Thing[] VALUES = {...}는 수정이 가능하다.

```java
// 캡슐화 예제
public class Capsule {
    private String name;
    private int cost;
    public float getDolorCost() {
        return 1050/cost;
    }
    
    public int getWonCost() {
        return cost;
    }
}
```

### 배열이 수정 가능한 문제를 해결할 수 있는 두 가지 해결책

1. 배열을 private으로 만들고, 불변 리스트를 추가
```java
private static final PAGE[] PAGE_INFO = {...};
public static final List<PAGE> VALUES = Collections.umodifiableList(Arrays.asList(PAGE_INFO));
```
- **읽기 전용**으로 response
- 원본이 바뀔 경우 같이 변경됨


2. 배열을 private로 두고, 복사본을 반환하는 public method
```java
public static final PAGE[] values() {
    return PAGE_INFO.clone();
}
```
- 해당 시점으로 clone 됨
- 원본이 바뀔 경우 같이 변경되지 않음


### 생각해볼만한 점

캡슐화는 꼭 한번 다시 점검해 보자.
접근제어자는 습관적으로 항상 최소로 사용하자.
