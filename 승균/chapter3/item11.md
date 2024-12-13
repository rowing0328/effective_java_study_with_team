## **item11: equals를 재정의하려거든 hashCode도 재정의하라**

## **핵심 주제**

**equals를 재정의한 클래스 모두에서 hashCode 재정의해야 한다.**
- equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체 hashCode 메서드는 몇 번을 호출해도 일관되게 값은 값 반환
- equals(Object) 두 객체가 같다고 판단, hashCode 똑같은 값을 반환
- equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서 다른 값 반환해야 성능 향상

## **hashCode 재정의 잘못했을 때 크게 문제가 되는 조항 두번째다. 즉 , 논리적으로 같은 객체는 같은 해시 코드를 반환해야한다.**

```java
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");
```

PhoneNumber 클래스는 hashCode를 재정의하지 않았기 때문에 논리적 동치인 두 객체가 서로 다른 해시코드를 반환하여 두 번째 규약 지키지 못한다.

**최악의 hashCode 구현 - 사용 금지!**
```java
@Override
public int hashCode() {
    return 42;
}
```

동치인 모든 객체에서 똑같은 해시코드를 반환하니 적법하다. <br/>
끔찍하게도 모든 객체에서 똑같은 값만 내어주므로 모든 객체가 해시 테이블의 버킷 하나에 담겨 마치 연결 리스트(linked list)처럼 동작 <br/>
결과 O(1) -> O(n) 느려짐.. <br/>
좋은 해시 함수라면 서로 다른 인스턴스에 해시 코드 반환 => hashCode 세번째 규약 <br/>

1. int 변수 result 선언한 후 값 c로 초기화한다. 이때 c는 해당 개체의 첫 번째 핵심 필드를 단계 
2. 해당 객체의 나머지 핵심 필드 f 각각에 대해 다음 작업을 수행
    - 해당 필드의 해시코드 c를 계산
      1. 기본 타입 필드라면, Type.hashCode(f)를 수행한다. 여기서 Type 해당 기본 타입의 박싱 클래스다.
      2. 참조 타입 필드면서 이 클래스 equals 메서드가 이 필드의 equals 재귀적으로 호출해 비교, 이 필드의 hashCode 재귀적으로 호출
        - 필드의 표준형을 만들어 hashCode 재귀적으로 호출, 필드의 값이 null이면 0을 사용
      3. 필드가 배열이면, 핵심 원소 각각을 별도 필드, 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산 아래 방식으로 갱신한다. 배열에 핵심 원소가 없다면 단순히 상수(0을 추천한다)를 사용한다.
    - 2.a 계산한 해시코드 c로 result 갱신
      1. result = 31 * result + c;
3. result를 반환

hashCode 다 구현했다면 이 메서드가 동치인 인스턴스에 대해 똑같은 해시코드 반환<br/>
파생 필드는 해시코드 계산에서 제외해도 된다. 즉, 다른 필드로부터 계산 해낼 수 있는 필드는 모두 무시 also 비교에 사용되지 않은 메소드 반드시 제외 

@AutoValue의 주요 특징

1. 자동 코드 생성:
- 클래스 정의 시 필드만 정의하면, @AutoValue가 나머지 메서드를 자동으로 생성합니다.
- getter, equals(), hashCode(), toString() 등 반복적으로 작성해야 하는 코드가 줄어듭니다.
2. 불변성(Immutable):
- AutoValue로 생성된 클래스는 기본적으로 불변 객체입니다.
- 필드 값이 변경되지 않으며, 데이터 안정성을 보장합니다.
3. Boilerplate 코드 감소:
- 데이터 객체에서 불필요한 코드를 작성할 필요가 없습니다.
- 데이터 객체 설계에만 집중할 수 있습니다.
4. Builder 패턴 지원:
- 복잡한 객체를 생성할 때 유용한 Builder 패턴을 지원합니다.
5. 확장 가능성:
- Gson, Jackson과 같은 JSON 라이브러리와 통합하여 JSON 직렬화/역직렬화를 간단히 처리할 수 있습니다.

```
import com.google.auto.value.AutoValue;
import com.google.gson.Gson;
import com.google.gson.TypeAdapter;

@AutoValue
public abstract class User {

    public abstract String getName();

    public abstract int getAge();

    // Gson TypeAdapter 생성
    public static TypeAdapter<User> typeAdapter(Gson gson) {
        return new AutoValue_User.GsonTypeAdapter(gson);
    }

    public static User create(String name, int age) {
        return new AutoValue_User(name, age);
    }
}
```

**전형적인 hashCode**
```java
@Override
public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```

비결정적(undeterministic) 요소는 전혀 없으므로 동치인 PhoneNumber 인스턴스들은 같은 해시 코드를 가짐<br/>
해당 코드는 PhoneNumber 딱 맞게 구현한 hashCode <br/>
자바 플랫폼 라이브러리 클래스들이 제공하는 hashCode 와 비교해도 손색없음 <br/>

해시 충돌이 더 적은 방법을 써야 한다면 구아바 com.google.common.hash.Hashing 참고

**한 줄 짜리 hashCode 메서드 - 성능이 살짝 아쉽다.**

```java
import java.util.Objects;

@Override
public int hashCode() {
   return Objects.hash(lineNum, prefix, areaCode);
}
```

입력중 기본 타입이 있으면 박싱과 언박싱이 거쳐야 해서 성능적으로는 좀 느림 <br/>
hash 메서드는 성능에 민감하지 않은 상황에서만 사용


**해시코드를 지연 초기화하는 hashCode 메서드 - 스레드 안정성까지 고려**
```java

private int hashCode; // 자동으로 0으로 초기화된다.

@Override
public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
    }
    return  result;
}
```
