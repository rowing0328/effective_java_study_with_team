## 아이템 33 - 타입 안정 이종컨테이너를 고려하라

### 동적 형 변환

이중 컨테이너를 사용하면 런타임에서도 타입 안전성을 보장할 수 있다.

특히, Class <T>를 활용해 타입 정보를 명시적으로 전달하고,

type.cast()를 통해 안전하게 형 변환을 수행한다.

예제 코드

```java
public <T> void putFavorite(Class<T> type, T instance) {
    favorites.put(Objects.requireNonNull(type), type.cast(instance));
}

Favorites favorites = new Favorites();
favorites.putFavorite(Game.class, new Game());
```

설명

HashSet <Integer>에 String을 넣는 것과 같은 타입 불일치 문제를 방지할 수 있다.

컴파일 시 강제적인 타입 체크는 하지 않지만, 런타임에 type.cast()를 통해 타입 오류를 예방한다.

### 타입 안정 이중 컨테이너

제네릭과 Map을 활용하여 다양한 타입의 데이터를 안전하게 저장하고 관리할 수 있는 구조이다.

Map <Class <?>, Object>를 기반으로 설계되며,

저장 시 타입 정보를 전달하고, 반환 시 요청한 타입으로 안전하게 변환하는 방식으로 동작한다.

이 방식은 다양한 타입을 유연하게 처리하면서도, 런타임 타입 오류를 방지할 수 있다는 점에서 큰 장점이 있다.

예제 코드

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public class Favorites {
    
    private Map<Class<?>, Object> favorites = new HashMap<>();

    // 특정 타입의 데이터를 저장
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }

    // 특정 타입의 데이터를 요청한 타입으로 반환
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
    
}
```

장점

-   타입 안정성  
    데이터를 Object 타입으로 저장하지만, 저장 시와 반환 시에 명시적인 타입 정보(Class <T>)를 사용하여 런타임 오류를 방지한다.
-   유연성  
    다양한 데이터 타입을 하나의 컨테이너에서 관리할 수 있다.  
    예를 들어, String, Integer, CustomClass 등 여러 타입을 동시에 저장하고 관리할 수 있다.