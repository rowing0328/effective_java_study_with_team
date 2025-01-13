## 📖 타입 안전 이종 컨테이너를 고려하라

### 핵심 내용

- 컬렉션 API로 대표되는 일반적인 제네릭 형태에서는 한 컨테이너가 다룰 수 있는 타입 매개변수의 수가 고정되어 있다.

- 하지만 컨테이너 자체가 아닌 키를 타입 매개변수로 바꾸면 이런 제약이 없는 타입 안전 이종 컨테이너를 만들 수 있다.

- 타입 안전 이종 컨테이너는 Class를 키로 쓰먀, 이런 식으로 쓰이는 Class 객체를 타입 토큰이라 한다.

- 또한, 직접 구현한 키 타입도 쓸 수 있다.

- 예컨대 데이터베이스의 행(컨테이너)을 표현한 DatabaseRow 타입에는 제네릭 타입인 Column<T>를 키로 사용할 수 있다.


## 💡 주요 내용 정리 & 🛠️ 실습 코드

제네릭은 Set<E>, Map<K,V> 등의 단일 원소 컨테이너에도 흔히 쓰인다.

이런 모든 쓰림에서 매개변수화되는 대상은 (원소가 아닌) 컨테이너 자신이다.

따라서 하나의 컨테이너에서 매개변수화할 수 있는 타입의 수가 제한된다.

컨테이러 대신 키를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하면 된다.

이렇게 하면 제네릭 타입 시스템이 값의 타입이 키와 같음을 보장해줄 것이다.

이러한 설계 방식을 타입 안전 이종 컨테이너 패턴이라 한다.



## 추가 강의 내용 정리

### 타입 안전 이종 컨테이너

Key가 wildcard type

```java
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

Get 할 때 요청받은 타입의 Value를 찾아 cast하여 response함
(Map에서 꺼내올 때는 Object)

### 동적 형 변환

```java
public <T> void putFavorite(Class<T> type, T instance) {
    favorites.put(Objects.requireNonNull(type), type.cast(instance));
}
```

```java
Favorites favorites = new Favorites();
Game game = favorites.putFavorite(Game.class, new Game());
```

HashSet<Integer>에 string을 넣는 것 같은 문제를 막을 수 있다.


### 슈퍼 타입 토큰 (ParameterizedTypeReference)

TypeReference, ParameterizedTypeReference 등이 있다.

```java
RestTemplate rt = new RestTemplate();
List<String> test = rt.exchange("http://localhost:8080", HttpMethod.GET, null, new ParameterizedTypeReference<List<String>>() {}).getBody();
```

## 🤔 생각 정리
- ....

