## 아이템 21: 인터페이스는 구현하는 쪽을 생각해 설계하라

## **핵심정리**

디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 재정의하지 않은 모든 클래스에서 사용하게 됨. but 모든 기존 구현체들과 매끄럽게 된다는 보장하기 어려움.

디폴트 메서드는 구현 클래스에 대해 아무것도 모른채 합의 없이 무작정 삽입된다.

자바 8의 Collection 인터페이스에서 추가된 removeIf 예로 , 메서드는 주어진 불리언 함수 (predicate:프레디키드) 가 true 반환하는 모든 원소 제거

**자바 8의 Collection 인터페이스에 추가된 디폴트 메서드**

```java
import java.util.Objects;

default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean result = false;
    for(Iterator<E> it = iterator(); it.hasNext(); ) {
        if(filter.test(it.next())) {
            it.remove();
            result = true;
        }
    }
    return result;
}
```

org.apache.commons.collections4.collection.SynchronizedCollection 아파치 커먼즈 라이브러리 클래스는 아파치 버전 클라이언트가 제공한 객체로 락을 거는 능력을 추가로 제공 <br/>
현재 SynchronizedCollection removeIf 메서드를 재정의 하고 있지 않기 때문에 removeIf를 쓰면 기능이 정상 동작 x

자바 플랫폼 라이브러리에서도 이런 문제를 예방하기 위해 일련의 조치를 취했다. 구현한 인터페이스의 디폴트 메서드를 재정의하고, 다른 메서드에서는 피폴트 메서드를 호출하기 전 필요한 작업을 수행하도록 만듬

Collections.synchronizedCollection 반환하는 package-private 클래스 remoteIf 재정의하고, 이를 호출하는 다른 메서드들은 디폴트 구현을 호출하기 전에 동기화를 했다.

**다른 메서드는 (컴파일에 성공하더라도) 기존 구현체에 런타임 오류를 일크일 수 있다.**

기존 인터페이스에 디폴트 메서드를 추가는 가능한한 최대한 피해서 하는게 좋다.
