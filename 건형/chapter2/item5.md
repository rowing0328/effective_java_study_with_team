## 📖 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

- DI !!

## 💡 주요 내용 정리 & 🛠️ 실습 코드

```java
import java.util.Objects;

public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChekcer(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
}
```

- 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋습니다.
- 이 자원들을 클래스가 직접 만들게 해서도 안됩니다. 
- 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹인 정적 팩터리나 빌더에) 넘겨주면 됩니다.
- 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해줍니다.

## 🤔 토론 및 질문
- 궁금한 점, 논의하고 싶은 내용
