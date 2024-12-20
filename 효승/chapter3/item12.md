※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.

## 아이템 12 - toString을 항상 override 하라

기본적으로 toString() 메서드는 객체의 클래스 이름과 @ 뒤에 16진수 해시코드를 결합한 문자열 형식(className@hashcode)을 반환한다.

---

### toString의 일반 규약

toString() 메서드는 간결하고 사람이 읽기 쉬운 형태로, 객체의 유익한 정보를 제공해야 한다.

객체의 상태를 표현하는 데 도움이 되는 필드 값을 포함시키는 것이 좋다.

---

### Lombok을 사용한 toString 구현

```java
@AllArgsConstructor
@ToString
public class Laptop {
    private String modelName;
    private String company;
}

// 출력 결과
Laptop(modelName=그램 16인치, company=삼성)
```

---

### 불필요한 변수가 있을 경우

```java
// 특정 변수를 제외하는 방법
@AllArgsConstructor
@ToString(exclude = {"modelName"})
public class Laptop {
    private String modelName;
    private String company;
}

// 더 나은 방법으로는 @ToString.Exclude 어노테이션 사용
@ToString
public class Laptop {
    @ToString.Exclude private String modelName;
    private String company;
}
```