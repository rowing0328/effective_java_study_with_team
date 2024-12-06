**※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.**

## 아이템 4 - 인스턴스화를 막으려거든 private 생성자를 사용하라

```java
public class BikeUtils {

    private BikeUtils() {
        throw new AssertionError();
    }

    // static method (유틸성)
    public static <T> T convertObject(..) {...}

}
```

-   `Human error 방지`  
    누군가(심지어 자신도) 실수로 인스턴스를 생성하지 않도록 private constructor를 사용한다.

-   `간단한 노력으로 안정성 확보`  
    private constructor를 선언하는 것은 많은 노력이 필요하지 않으며, 실수를 방지하는 확실한 방법이다.

-   `Util 클래스의 경우 생략 가능`  
    관용적으로 인스턴스를 생성하지 않는 클래스(예: 유틸리티 클래스)라면, 경우에 따라 생략하기도 한다.