## **item6: 불필요한 객체 생성을 피하라**

## **핵심 주제**

```java
String s = new String("bikini");
```
해당 문장은 실행될 때마다 String 인스턴스를 만들어 인스턴스 낭비가 많이됨.


성능을 훨씬 더 끌어올릴 수 있다.
```java
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X{CL}|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

**String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합x**
<br/>
Pattern 인스턴스는 한 번 쓰고 버려져서 곧바로 가비지 컬렉션 대상이 된다.

```java
import java.util.regex.Pattern;

public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})" + "(X{CL}|L?X{0,3})(I[XV]|V?I{0,3})$");
    
    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```
pattern 인스턴스를 클래스 초기화 과정에서 직접 생성해 캐싱, 나중에 isRomanNumeral 메서드가 호출될 때마다 인스턴스를 재사용

**오토방식은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아니다.**

**박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자**