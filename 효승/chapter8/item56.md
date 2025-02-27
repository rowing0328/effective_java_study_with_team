## 아이템 56 - 공개된 API 요소에는 항상 문서화 주석을 작성하라

### 문서화 주석의 중요성

공개된 API는 다양한 개발자가 사용할 수 있기 때문에, 명확한 문서화 주석이 필수적이다.

주석은 사용자가 API를 올바르게 이해하고 사용할 수 있도록 도와준다.

### 현실적인 접근 방법

모든 API에 완벽한 주석을 작성하는 것은 현실적으로 어려운 일이다.

따라서 아래와 같은 방식으로 주석을 점진적으로 보완하는 것이 좋다.

-   가장 중요한 요소부터 시작  
    메서드의 반환값, 매개변수, 예외 처리 등 핵심 요소에 우선적으로 주석을 작성한다.
-   점진적으로 개선  
    시간이 허락될 때마다 주석을 추가하거나 기존 주석을 개선한다.

예제 코드 - 주석 작성 예시

```java
/**
 * 사용자의 이름 목록을 반환합니다.
 *
 * @return 사용자의 이름이 포함된 리스트 (비어 있을 수 있음)
 * @throws DataAccessException 데이터 접근 중 오류가 발생한 경우
 */
public List<String> getUserNames() {
    // 메서드 구현
}
```