## 아이템 5 - 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

```
@Configuration
public class PhonePatternChecker {

    private final String pattern = "\\d{10}"; // 고정된 패턴

    public boolean isValid(String phone) {
        return phone.matches(pattern);
    }
}

@Configuration
public class PhonePatternChecker {

    private final String pattern;

    public PhonePatternChecker(String pattern) { // 생성자 패턴
        this.pattern = pattern;
    }

    public boolean isValid(String phone){ ... }

}
```

-   `유연성`  
    외부에서 값을 주입받아 코드 수정 없이 다양한 상황에서 재사용할 수 있다.

-   `환경별 대응`  
    application.yml 설정을 통해 환경(live, dev, test)에 따라 다른 값을 쉽게 적용할 수 있다.

-   `테스트 편의성`  
    테스트 작성 시 Mock 데이터나 테스트용 패턴을 쉽게 주입할 수 있어 코드가 독립적이고 테스트 친화적이다.