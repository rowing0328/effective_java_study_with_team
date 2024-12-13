## **📖item14: Comparable 구현할지 고려해라**

## **💡핵심 주제**
comparable 인터페이스의 유일무이한 메서드인 compareTo <br/>
Comparable 구현했다는 것은 클래스의 인스턴들에는 자연적인 순서 있음 <br/>
Comparable 구현 객체들의 배열 손쉽게 정렬

Arrays.sort(a);

검색, 극단값 계산, 자동 정렬되는 컬렉션 관리도 쉽게 할 수 있음 <br/>
아래 코드는 종복 제거 후, 알파벳 순으로 출력 why? String이 Comparable 구현

```java
import java.util.Collections;

public class WordList {
    public static void main(String[] args) {
        Set<String> s = new TreeSet<>();
        Collections.addAll(s, args);
        System.out.println(s);
    }
}
```

Comparable 구현하여, 인터페이스를 활용하는 수많은 제네릭 알고리즘과 컬렉션의 힘을 누릴 수 있다. <br/>
알파벳, 숫자, 연대 같이 순서가 명확한 값은 반드시 Comparable 인터페이스 구현하자. <br/>

```java
public interface Comparable<T> {
    int compareTo(T t);
}
```

**compareTo 메서드의 일반 규약은 equal 규약과 비슷함**

객체의 순서를 비교한다. 주어진 객체보다 작으면 음의 정수, 같으면 0, 크면 양수를 반환 but 객체와 비교 할 수 없으면 ClassCastException

1. Comparable 구현 클래스는 x,y 대하여 sgn(x.compareTo(y)) == -sgn(y.compare(x))
2. Comparable 구현한 클래스는 추이성 보장 => 즉, (x.compareTo(y) > 0 && y.compareTo(z) > 0) 이면 x.compareTo(z) > 0 이다.
3. Comparable 구현한 클래스는 모든 z에 대해 x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z)) 이다.
4. 필수는 아니지만 지키면 좋음 (x.compareTo(y) == 0) == (x.equals(y))여야 한다. (꼭 지키는 것을 권함) => why? collection 동치성 비교 할 때, compareTo를 사용

comparable 규약을 지키지 못할 시, 정렬된 컬렉션 TreeSet and TreeMap, 검색 정렬 알고리즘으로 활용되는 유틸리티 Collections 와 Arrays 기능 사용 못함. (정상작동 ❌)

우회 방법도 똑같음. 컴포지션을 사용하여 상속인 아닌 객체를 필드값으로 두자. <br/>

**BigDecimal 클래스 예시** <br/>
new BigDecimal("1.0") and new BigDecimal("1.00") 추가 <br/>
equals 비교시 다름 but HashSet and TreeSet 사용하면 원소 하나만 가짐 because compareTo로 비교

Comparable 인수로 받는 제네릭 인터페이스 compareTo 메서드의 인수 타입은 컴파일타임에 정함. 즉, 입력 인수의 타입을 확인하거나 형변환 필요 ❌ <br/>
객체 참조 필드 비교 시, compareTo 메서드를 재귀적으로 호출함. Comparable 구현하지 않은 필드나  표준이 아닌 순서로 비교해야 한다면 비교자(Comparator) 사용

**객체 참조 필드가 하나뿐인 비교자**
```java
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString> {
    public int compareTo(CaseInsensitiveString cis) {
        return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
    }
}
```

CaseInsensitiveString<CaseInsensitiveString> 구현에 집중 => CaseInsensitiveString 참조는 CaseInsensitiveString 비교 가능 🙆

가장 핵심이 되는 필드가 똑같다면, 똑같지 않은 필드를 찾을 때까지 그다음으로 중요한 필드를 비교해나간다.

**기본 타입 필드가 여럿일 때의 비교**
```java
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode); // 가장 중요한 필드
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix); // 두 번째로 중요한 필드
        if(result == 0) {
            result = Short.compare(lineNum, pn.lineNum); // 세 번째로 중요한 필드
        }
    }
    return result;
}
```

자바 8에서는 Comparator 인터페이스가 일련의 비교자 생성 메서드(comparator construction method)와 팀을 꾸려 메서드 연쇄 방식으로 비교자를 생성 가능 <br/>
해당 방식은 약간의 성능 저하가 뒤따름 <br/>

**비교자 생성 메서드를 활용한 비교**
```java
private static final Comparator<PhoneNumber> COMPARATOR = comparingInt((PhoneNumber pn) -> pn.areaCode)
                                                                .thenComparaingInt(pn -> pn.prefix)
                                                                .thenComparaingInt(pn -> pn.lineNum);

public int compareTo(PhoneNumber pn) {
    return COMPARATOR.compare(this, pn);
}
```

**해시코드 값의 차를 기준으로 하는 비교자 - 추이성을 위배한다!**

```java
import java.util.Comparator;

static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return o1.hashCode() - o2.hashCode();
    }
};
```

**이 방식은 사용하면 안됨. 정수 오버플로를 일으키거나 부동소수점 계산 방식에 따른 오류 발생 가능**

**정적 compare 메서드를 활용한 비교자**
```java
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return Integer.compare(o1.hashCode(), o2.hashCode);
    }
};
```

**비교자 생성 메서드를 활용한 비교자**
```
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```