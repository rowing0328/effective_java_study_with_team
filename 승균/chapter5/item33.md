## 타입 안전 이종 컨테이너를 고려하라.

## **핵심주제**
제네릭은 Set<E>, Map<K, V> 등의 컬렉션과 ThreadLocal<T>, AtomicReference<T> 등 단일 원소 컨테이너에도 흔히 쓰인다. 

이런 모든 쓰임에서 매개변수화되는 대상은 (원소가 아닌) 컨테이너 자신이다.

따라서 하나의 컨테이너에서 매개변수화할 수 있는 타입의 수가 제한된다.

컨테이너의 일반적인 용도에 맞게 설계된 것이니 문제 될건 없다. 

Set에는 원소의 타입을 뜻하는 단 하나의 타입 매개변수만 있으면 되며, Map에는 키와 값의 타입을 뜻하는 2개만 필요한 식이다.

데이터베이스의 행(row)은 임의 개수의 열(column)을 가질 수 있는데, 모두 열을 타입 안전하게 이용 할 수 있다면 awesome << 가능하다고 함 대박입니다유

컨테이너 대신 키를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하면 된다. 

컨테이너에 값을 넣거나 뺼 때 매개변수화한 키를 함께 제공하면 된다. 

이렇게 하면 제네릭 타입 시스템이 값의 타입이 키와 같음을 보장해줄 것이다. 

이러한 설계 방식을 타입 안전 이종 컨테이너 패턴이라 한다.

컴파일타임 타입 정보와 런타임 타입 정보를 알아내기 위해 메서드들이 주고받는 class 리터럴을 타입 토큰(type token)이라 한다. 

**코드 33-1 타입 안전 이종 컨테이너 패턴 - API**
```java
public class Favorites {
    public <T> void putFavorite(Class<T> type, T instance);
    public <T> T getFavorite(Class<T> type);
}
```

Favorites 클래스를 사용하는 예시다. 

즐겨 찾는 String, Integer, Class 인스턴스 저장, 검색, 출력하고 있다.

**코드 33-2 타입 안전 이종 컨테이너 패턴 - 클라이언트**
```java
public static void main(String[] args) {
    Favorites f = new Favorites();
    
    f.putFavorite(String.class, "java");
    f.putFavorite(Integer.class, 0xcafebabe);
    f.putFavorite(Class.class, Favorites.class);
    
    String favoriteString = f.getFavorite(String.class);
    int favoriteInteger = f.getFavorite(Integer.class);
    Class<?> favoriteClass = f.getFavorite(Class.class);

    System.out.printf("%s %s %n", favoriteString, favoriteInteger, favoriteClass.getName());
}
```

Favorites 인스턴스 타입 안전하다. 

String 요청했는데 Integer 반환하는 일은 절대 없다. 또한 모든 키의 타입이 제각각이라, 일번적인 맵과 달리 여러 가지 타입의 원소를 담을 수 있다. 

따라서 Favorites 타입 안전 이종(heterogeneous) 컨테이너라 할 만하다.

```
public class Favorites {
    private Map<Class<?>, Object> favorites = new HashMap<>();
    
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }
    
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
    
}
```

Favorites가 사용하는 private 맵 변수인 favorites의 타입은 Map<Class<?>, Object>이다. 

비한정적 와일드카드타입이라 이 맵 안에 아무것도 넣을 수 없다고 생각할 수 있지만, 사실은 그 반대다. 

와일드카드 타입이 중첩(nested)되었다는 점을 깨달아야 한다. 맵이 아니라 키가 와일드카드 타입인 것이다. 

이는 모든 키가 서로 다른 매개변수화 타입일 수 있다는 뜻으로, 첫 번째는 Class<String> , 두 번째는 Class<Integer>식으로 될 수 있다. 

다양한 타입을 지원하는 힘은 여기서 나온다. 

favorites 맵의 값 타입은 단순히 Object라는 것이다. 

무슨 의미? : 맵은 키와 값 사이의 타입 관계를 보증하지 않는다는 말이다. 

즉, 모든 값이 키로 명시한 타입임을 보증하지 않는다.

사실 자바의 타입 시스템에서는 이 관계를 명시할 방법이 없다. 

하지만 우리는 이 관계가 성립함을 알고 있고, 즐겨찾기를 검색할 때 그 이점을 누리게 된다. 

putFavorite 구현은 아주 쉽다. 주어진 Class 객체와 즐겨찾기 인스턴스 favorites에 추가해 관계를 지으면 끝이다. 

말했듯이, 키와 값 사이의 '타입 링크(type linkage)'정보는 버려진다. 

getFavorite 코드는 putFavorite보다 강조해두었다. 

먼저, 주어진 Class 객체에 해당하는 값을 favorites 맵에서 꺼낸다. 이 객체가 바로 반환해야 할 객체가 맞지만, 잘못된 컴파일타임 타입을 가지고 있다. 

이 객체의 타입은 Object, 우리는 이를 T로 바꿔 반환해야 한다. 

getFavorite 구현은 Class의 cast 메서드를 사용해 이 객체 참조를 Class 객체가 가리키는 타입으로 동적 형변환한다.

cast 메서드는 형변환 연산자의 동적 버전이다. 

이 메서드는 단순히 주어진 인수가 Class 객체가 알려주는 타입의 인스턴스인지를 검사한 다음, 맞다면 그 인수를 그대로 반환하고, 아니면 ClassCastException

클라이언트 코드가 깔끔히 컴파일 된다면 getFavorite이 호출하는 cast는 ClassCastException을 던지지 않을 것임을 우리는 알고 있다. 

favorites 맵 안의 값은 해당 키의 타입과 항상 일치

```java
public class Class<T> {
    T cast(Object obj);
}
```

T로 비검사 형변환하는 손실 없이 Favorites를 타입 안전하게 만드는 비결

Favorites 클래스에는 알아두어야 할 제약 두가지
1. 악의적인 클라이언트가 Class 객체로 로타입 넘기면 Favorites 타입 안전성이 꺠짐 => 이렇게 짜여지면 컴파일 할 때, 비검사 경고
2. HashSet과 HashMap 등의 일반 컬렉션 구현체에도 똑같은 문제가 있다. HashSet의 로 타입을 사용하면 HashSet<Integer>에 String 넣는 건 아주 쉬운 일이다.