## 아이템 22 - 인터페이스는 타입을 정의하는 용도로만 사용하라

Constant static final은 Anti-Pattern

클래스 내부에서 사용하는 static final 상수는 내부 구현에 해당한다.

이 상수를 외부에서 접근 가능하게 만들면 혼란을 초래할 수 있다.

만약 상수를 여러 곳에서 사용해야 한다면 Util Class를 활용하는 것이 더 적합하다.

코드 예제

```java
// 잘못된 사용 예시
public class OrderService {
	public static final double SECOND_TO_MIN = 60;
}

// 올바른 사용 예시
public class TimeConvertUtil {

	public static final double SECOND_TO_MIN = 60;
    
    public static double secondToMin(double second) {
    	return second / SECOND_TO_MIN;
    }
    
}
```

### Static Import 사용 시 주의사항

static import를 사용하면 클래스 이름 없이 상수를 바로 사용할 수 있다.

하지만 잘못 사용하면 혼란을 초래할 수 있으니 신중하게 사용하는 것이 좋다.

코드 예제

```
// 일반 import
import example.chapter4.TimeConvertUtil;
TimeConvertUtil.SECOND_TO_MIN;

// static import
import static example.chapter4.TimeConvertUtil.SECOND_TO_MIN;
SECOND_TO_MIN;
```

권장 사항

일반 import 방식을 사용하는 게 더 좋다.

이 방식은 비슷한 이름의 상수(enum 포함)와 충돌할 위험을 줄여준다.

상수를 외부에서 관리해야 한다면 Properties에 값을 선언하고

의존성 주입(Dependency Injection)을 활용하는 것도 좋은 방법이다.
