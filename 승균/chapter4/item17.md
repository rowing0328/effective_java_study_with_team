# item17: 변경 가능성을 최소화하라

## **핵심 정리**
불변 클래스란 그 인스턴스 내부 값을 수정할 수 없는 클래스<br/>
생성~파괴까지 절대 달라지지 않음 <br/>
String, 기본 타입의 박싱 클래스, BigInteger, BigDecimal 불변 클래스이다. 불변 클래스의 장점은 클래스보다 설계하고 구현 및 사용이 쉬움. 오류 가능성도 ⬇

**클래스 불변을 만들기 위한 다섯가지 규칙**
1. 객체 상태 변경하는 메서드 제공 ❌
2. 클래스 확장 할 수 없도록 함. -> 하위 클래스에서 나쁜의도로 객체 상태 변하게 만드는 사태 방지
3. 모든 필드를 final 선언 -> 스레드에도 안전함
4. 모든 필드를 private 선언 -> 가변 객체를 클라이언트가 직접 수정하는 일을 막아줌
️️5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다. => 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야함. 참조 값을 가리키면 바꿀 수 있기 때문에 대참사 <br/>
위에 경우 생성자, 접근자, readObject 메서드에서 방어적 복사를 수행하자


```java
public final Class Complex{
    private final double re;
    private final double im;
    
    public Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }
    
    public double realPart() { return re; }
    public double imaginaryPart() { return im; }

    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }
    
    public Complex minus(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }
    
    public Complex time(Complex c) {
        return new Complex(re * c.re + im * c.im, re * c.im + im * c.re);
    }
    
    public Complex dividedBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp, (im * c.re - re * c.im) / tmp);
    }
    
    @Override
    public boolean equals(Object o) {
        if(o == this) return true;
        if(!(o instanceof Complex)) return false;
        Complex c = (Complex) o;
        
        return Double.compare(c.re, re) == 0 && Double.compare(c.im, im) == 0;
    }
    
    @Override
    public int hashCode() {
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }
    
    @Override
    public String tostring() {
        return "(" + re + " + " + im + "i)";
    }
    
}
```

사칙연산 메서드(plus, minus, times, dividedBy) 정의 <br/>
위에 처럼 인스턴스 자신 수정 x 새로운 Complex 인스턴스 만들어 반환 <br/>
피연산자에 함수를 적용해 결과 반환, 피연산자 자체는 그대로인 프로그래밍 패턴을 함수형 프로그래밍 <br/>
절차적 혹은 명령형 프로그래밍에서는 피연산자인 자신을 수정해 자신의 상태가 변하게 된다. <br/>

**불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다**

여러 스레드가 동시에 사용해도 절대 훼손되지 않는다.<br/>
이러한 점 덕분에 불변 객체는 안심하고 공유할 수 있다. <br/>

```java
public static final Complex ZERO = new Complex(0,0);
public static final Complex ONE = new Complex(1, 0);
public static final Complex I = new Complex(0, 1);
```

불변 객체는 자주 사용되는 인스턴스를 캐싱하여 같은 인스턴스가 중복 생성하지 않게 정적 팩터리를 제공 할 수 있음.<br/>
위에 처럼 사용하면 메모리 사용량과 가비지 컬렉션 비용이 줄어든다. <br/>
불변 객체는 자유롭게 공유 할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다. <br/>

**객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.**

값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 그 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월 <br/>
validation check 로직등 메소드로 따로 분리해서 사용할 수 있기 때문이라는 느낌 정확하지는 않음.

**불변 객체는 그자체로 실패 원자성을 제공**

절대로 변하지 않으니 잠깐이라도 불일치 상태에 빠질 가능성이 없다. 

**불변 클래스에도 단점은 있다. 값이 다르면 반드시 독립된 객체로 만들어야함**
메모리를 많이 차지함.  => BigInteger에서 비트하나만 바꿔도 백만 비트짜리 인스턴스를 생성해야 한다. 

다단계연산들을 에측하여 기본기능으로 제공하는 방법 => 더 이상 각 단계마다 객체 새로 생성 할 필요 없음. 
BigInteger 모듈러 지수 같은 다단계 연산 속도를 높여주는 가변 동반 클래스 package-private 

**상속을 못하게 하는 가장 좋은 방법 생성자를 private**

````java
public class Complex {
    private final double re;
    private final double im;
    
    private Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }
    
    public static Complex valueOf(double re, double im) {
        return new Complex(re, im);
    }
}
````

패키지 밖에서 본 위 클래스는 불변 객체 사실상 final

무조건 setter 사용 x 클래스는 꼭 필요한 경우가 아니라면 불변이어야함. 

**모든 클래스는 불변으로 만들 수 없지만 변경 할 수 있는 부분을 최대한 줄여서 코딩**

합당한 이유가 없다면 모든 필드는 private final이어야 함.<br/>
**생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.**