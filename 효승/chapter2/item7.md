## 아이템 7 - 다 쓴 객체 참조를 해제하라

```java
Map<Object, String> cache = new HashMap<>();
Object key = new Object();
cache.put(key, "Value");

// key가 더 이상 필요 없음
key = null; // 하지만 cache에는 여전히 key와 value가 남아 있음
```

-   `메모리 누수 발생`    
    사용이 끝난 객체가 참조 상태로 남아 있으면 GC가 해당 객체를 회수하지 못한다.

-   `성능 저하`  
    불필요한 객체가 메모리를 차지하면서 시스템 성능이 떨어진다.

-   `OutOfMemoryError 발생 위험`  
    메모리 누수가 쌓이면 자원이 부족해지고, 심각한 경우 애플리케이션이 멈출 수 있다.

<br>

```java
import java.util.WeakHashMap;

public class WeakHashMapExample {
    
    public static void main(String[] args) {
        WeakHashMap<Object, String> cache = new WeakHashMap<>();
        Object key = new Object();
        cache.put(key, "Value");

        key = null; // 참조 제거
        System.gc(); // GC 실행 요청

        System.out.println("Cache size: " + cache.size()); // 0
    }
    
}
```

-   `명시적 참조 제거`  
    객체 사용이 끝난 후 컬렉션에서 제거해야한다.

-   `약한 참조 활용`  
    WeakHashMap으로 다 쓴 객체를 자동으로 정리할 수 있다.

-   `캐시 관리`  
    유효 기간을 설정해 오래된 항목을 자동으로 제거한다.  
    불필요한 항목은 주기적으로 정리하는게 좋다.