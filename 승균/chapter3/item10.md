## **📖item10: equals는 일반 규약을 지켜 재정의하라**
equals 메서드는 쉬워보이지만 곳곳에 함정이 도사리고 있어서 끔찍한 결과를 초래함. -> 문제 회피 가장 쉬운 길은 아예 재정의 x
HashCode, Equals 재정의 하는게 좋은줄 알았는데.. 잘못알고 있었음.
## **💡핵심 주제**

### **재정의 하지 않아도 되는 경우**
1. 각 인스턴스가 본질적으로 고유 
    - 값 표현 x 동작하는 객체 표현 
    - Thread -> Object의 equals 이러한 클래스 용도 맞게 구현
2. 인스턴스 '논리적 동치성(logical equality)' 검사 x
    - 두 Pattern의 인스턴스가 같은 정규 표현식 나타내는지를 검사 방법 즉, 논치적 동치성 검사 방법 => equals 필요
    - 위에 방식이 필요하지 않다고 설계자가 후자로 판단 Object 기본 equals 해결
3. 상위 클래스에서 재정의 equals 하위 클래스에도 딱 맞음
   - Set 구현체 AbstractSet 구현한 equals 상속, List 구현체 AbstractList, Map 구현체 AbstractMap
4. 클래스가 private 이거나, package-private equals 호출 x
    - equals 실수 방지 하기 위해 아래 코드 작성해도 좋음

```java
@Override public boolean equals(Object o) {
    throw new AssertionError();
}
```

## **재정의 해도 되는 경우**
- 객체 식별성 x, 논리적 동치성을 확인하고 싶을 때, 재정의
- 값 클래스 Integer, String 값이 같은지 확인하고 싶은 경우
- 값 클래스여도 2개 이상 생성 되지 않는 인스턴스라면 재정의 x 


**equals 메서드 동치 관계를 구현, 아래를 만족**
- 반사성(reflexivity): null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true
- 대칭성(symmetry): null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)가 true면 y.equals(x)도 true
- 추이성(transitivity): null이 아닌 모든 참조 값 x,y,z에 대해 x.equals(y)가 true 이고 y.equals(z) 면 x.equals(z)도 true 다.
- 일관성(consistency): null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true 반환하거나 항상 false 반환
- null-아님: x.equals(null)은 false

동치 관계 : 집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산

반사성 : 객체는 자기 자신과 같아야함.

대칭성 : 두 객체는 서로에 대한 동치 여부에 대해 똑같이 답해야함.


## **잘못된 코드 - 대칭성 위배!**

```java
import java.util.Objects;

public final class CaseInsensitiveString {
    private final String s;

    public CaseInsensitiveString(String s) {
        this.s = Objects.requireNonNull(s);
    }
    
    @Override
    public boolean equals(Object o) {
        if(o instanceof CaseInsensitiveString)
            return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        if(o instanceof String)
            return s.equalsIgnoreCase((String) o);
        return false;
    }
    
}
```

CaseInsensitiveString cis = new CaseInsensitiveString("Polish");<br/>
String s = "polish";

cis.equals(s) => true <br/>
s.equals(cis) => false 반환하여, 대칭성을 명백히 위반

```java
List<CaseInsensitiveString> list = new ArrayList<>();
list.add(cis);
```

list.contains(s) 호출하면 false 반환<br/>
위에 코드는 추이성을 어긴 코드이다.

**추이성**: 첫 번째 객체 and 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야함.

```java
public class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    public boolean equals(Object o) {
        if(!(o instanceof Point)) return false;
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
}

```

**color equals**
```java
@Override
public boolean equals(Object o) {
    if(!(o instanceof ColorPoint)) {
        return false;
    }
    return super.equals(o) && ((ColorPoint) o).color == color;
}
```

Point p = new Point(1, 2); <br/>
ColorPoint p = new ColorPoint(1, 2, Color.RED);

p.equals(cp)는 true, cp.equals(p) false 반환

```java
@Override
public boolean equals(Object o) {
    if(!(o instanceof Point)) return false;
    
    if(!(o instanceof ColorPoint)) return o.equals(this);
    
    return super.equals(o) && ((ColorPoint) o).color == color;
}
```

ColorPoint p1 = new ColorPoint(1, 2, Color.RED);<br/>
ColorPoint p2 = new Point(1, 2); <br/>
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);

p1.equals(p2) true, p2.equals(p3) true, p1.equals(p3) false => 추이성 위배 why? p1 and p2 색상까지 고려

이 현상은 모든 객체 지향 언어의 동치 관계에서 나타나는 문제점. <br/>
**구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족 시킬 방법은 존재 x**

**리스코프 치환 원칙**
```java
@Override
public boolean equals(Object o) {
    if (o == null || o.getClass() != getClass()) return false;
    
    Point p = (Point) o;
    return p.x == x && p.y == y;
}
```

같은 구현 객체와 비교할 때만 true 반환. Point 하위 클래스는 정의상 Point이므로 어디서든 Point 활용될 수 있어야함.

**이를 해결하는 방법으로는 상속 대신 컴포지션을 사용**
컴포지션: 한 클래스가 다른 클래스의 객체를 **“포함”**하는 방식으로 동작을 재사용하거나 확장하는 설계 기법<br/>

상속(Inheritance): “is-a” 관계를 나타냄.<br>
• A가 B를 상속받으면 A는 B이다. (예: Penguin is a Bird) <br>
컴포지션(Composition): “has-a” 관계를 나타냄.<br>
• A가 B를 포함하면 A는 B를 가지고 있다. (예: Car has a Engine)

```java
import java.util.Objects;

public class ColorPoint {
    private final Point point;
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        this.point = new Point(x, y);
        this.color = Objects.requireNonNull(color);
    }
    
    public Point asPoint() {
        return point;
    }
    
    @Override
    public boolean equals(Object o) {
        if(!(o instanceof ColorPoint)) return false;
        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }
    
}
```

**일관성** : 두 객체가 같다면 앞으로도 영원히 같아야함. 불변 객체라면 다르면 끝까지 달라야함.<br/>
클래스가 불변이든 가변이든 equals 판단에 신뢰 x 자원 끼어들면 안됨 <br/>
null-아님: 모든 객체가 null과 같으면 안됨

```java
@Override
public boolean equals(Object o) {
    if(!(o instanceof MyType)) return false;
    MyType mt = (MyType) o;
}
```
istanceof는 피연산자가 첫 번째 피연산자 null이면 false 반환 

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환한다.
4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.

float와 double 제외 기본 타입 필드 == 연산자로 비교, 참조 타입 필드 equals 메서드 <br/>
flat와 double 필드는 각각 정적 메서드인 Float.compare(float, float) or Double.compare(double, double)로 비교한다.

float, double 특별 취급하는 이유는 Float.NAN , -0.0f 특수한 부동소수 값 다뤄야 하기 때문이다.

어떤 필드를 먼저 비교하느냐가 equals 성능 좌우함. <br/>
최상의 성능을 바란다면 다를 가능성이 크거나, 비교하는 비용이 싼 필드를 먼저 비교

**전형적인 equals 메서드 예**
```java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;
    
    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix =  rangeCheck(prefix, 999, "프리픽스");
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
    }
    
    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max) 
            throw new IllegalArgumentException(arg + ": " + val);
        return (short) val;
    }
    
    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if(!(o instanceof PhoneNumber)) return false;
        PhoneNumber pn = (PhoneNUmber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
    
}
```

- equals 재정의 시, 반드시 hashCode도 재정의
- 너무 복잡하게 해결 x -> 필드들의 동치성만 검사해도 equals 규약 o
- Object 외의 타입을 매개 변수로 받는 equals 메서드 선언 x