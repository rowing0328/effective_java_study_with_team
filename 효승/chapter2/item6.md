## 아이템 6 - 불필요한 객체 생성 금지

```java
 // Boxing type
public static long sum() {
	Long sum = 0L;
    for(long i = 0; i <= Integer.MAX_VALUE; i++) {
    	sum += i;
    }
    return sum;
}

// Primitive Type
public static long sum() {
	long sum = 0L;
    for(long i = 0; i <= Integer.MAX_VALUE; i++) {
    	sum += i;
    }
    return sum;
}
```

-   `Boxing type 대신 Primitive Type 을 권장한다.`  
    반복문이나 연산에서는 성능 저하를 유발할 수 있으므로 항상 Primitive Type을 우선적으로 사용하자.

<br>

```java
public class PhonePatternUtil {
	
    private final String pattern;
    
    public boolean isValid(String phone) {
    	...
    }

}
```

-   `Util Class 에서 또한 Primitive type을 권장한다.`  
    참 거짓을 response 하는데 Boxing type을 사용하는 것은 낭비일 수 있다.

<br>

```java
// Primitive Type 사용
int price;  // 가격이 0원 (예: 증정품)

// Boxing Type 사용
Integer price = null; // 가격이 아직 정해지지 않음
```

-   `상황에서는 Boxing Type(Wrapper Class)이 더 적합할 수 있다.`  
    -   Primitive Type (int)
    -   사용 조건 : 기본값이 명확하고, 성능 최적화가 필요한 경우
    -   예 : 가격이 0원으로 설정된 증정품
-   `Boxing Type (Integer)`  
    -   사용 조건 : null 값을 통해 설정되지 않은 상태를 명확히 표현해야하는 경우
    -   예 : 가격이 아직 정해지지 않은 상태

<br>

```java
// 비효율적인 코드: Pattern instance가 매번 생성됨
static boolean isEmailValid(String s) {
    return s.matches("[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,6}$");
}

// String.matches 내부 구현(String.java)
public boolean matches(String regex) {
    return Pattern.matches(regex, this);
}

// Pattern.matches 내부 구현 (Pattern.java)
public static boolean matches(String regex, CharSequence input) {
    Pattern p = Pattern.compile(regex); // Pattern 객체 생성
    Matcher m = p.matcher(input);
    return m.matches();
}

// 효율적인 코드: Pattern instance를 한 번만 생성
public class EmailUtil {

    private static final Pattern EMAIL = Pattern.compile("[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,6}$");

    static boolean isEmailValid(String s) {
        return EMAIL.matcher(s).matches();
    }
    
}
```

-   `주의해야 할 내장 Method`  
    자주 사용하는 내장 메서드는 성능과 메모리 사용을 고려해, 재사용 가능한 구조로 변경하자.