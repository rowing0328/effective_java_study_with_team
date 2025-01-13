## 인터페이스는 타입을 정의하는 용도로만 사용하라

## 핵심정리
인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입 역할 가능

상수 인터페이스를 사용하지 말자..! 

**상수 인터페이스 안티패턴 - 사용금지!**
```java
public interface PhysicalConstants {
    // 아보가드로 수 (1/몰)
    static final double AVOGADROS_NUMBER = 6.022_140_856e23;
    
    // 볼츠만 상수 (J/K)
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    
    // 전자 질량 (kg)
    static final double ELETRON_MASS = 9.109_383_56e-31;
}
```

상수 인터페이스를 구현하는 것은 내부 구현을 클래스의 API로 노출 시키는 행위이다. <br/>
클래스가 어떤 상수 인터페이스를 사용하든 사용자에게는 아무런 의미가 없다. <br/>
오히려 사용자에게 혼란을 주기도 하며, 더 심하게는 클라이언트 코드가 내부 구현에 해당하는 이 상수들에게 종속되게함 <br/>

상수를 공개할 목적이라면 더 합당한 선택지가 몇 가지 있다. 특정 클래스나 인터페이스와 강하게 연관된 상수라면 클래스나 인터페이스 자체에 추가 <br/>
모든 숫자 기본 타입의 박싱 클래스가 대표적으로, Integer and Double에 선언된 MIN_VALUE, MAX_VALUE 상수가 대표적인 예이다. <br/>

열거 타입으로 나타내기 적합한 상수라면 열거 타입으로 만들어 공개하면 된다. <br/>

```java
public class PhysicalConstants {
    private PhysicalConstants() { } // 인스턴스화 방지
    
    // 아보가드로 수 (1/몰)
    public static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    // 볼츠만 상수 (J/K)
    public static final double BOLTZMANN_CONST = 1.380_648_52e-23;
    // 전자 질량 (kg)
    public static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```