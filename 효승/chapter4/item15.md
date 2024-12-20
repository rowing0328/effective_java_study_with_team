※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.

## 아이템 15 - 클래스와 멤버의 접근 권한을 최소화하라

### public class의 instance field

-   `Public으로 열 경우 Thread-safe하지 않음`  
    클래스의 인스턴스 필드를 public으로 열어두면 외부에서 필드에 직접 접근이 가능해진다.  
    이로 인해 여러 스레드에서 동시에 접근할 경우 데이터 무결성을 보장할 수 없으며,  
    Lock 규칙을 적용하기 어려워진다.
-   `필요한 경우에만 public static final 사용`  
    상수는 public static final로 선언하여 외부에서 읽기만 가능하도록 설계할 수 있다.  
    예를 들어, ITZIP\_BASE\_PAGE와 같은 상수는 공개 가능하다.
-   `public static final 배열은 불변하지 않음`  
    배열은 public static final로 선언하더라도, 배열 요소는 외부에서 수정할 수 있다.  
    따라서 배열은 외부에 노출하기 전에 복사본을 반환하거나, 불변 리스트로 변환하여 제공해야 한다.

`코드 예제`

```java
// 환율 계산
public class Capsule {

	private String name;
    private int cost;
    
    public float getDolorCost() {
    	return 1050 / cost;
    }
    
    public int getWonCost() {
    	return cost;
    }
    
}
```

`설명`

name과 cost 필드는 private으로 선언되어 외부에서 직접 접근할 수 없다.

필드 값이 필요한 경우, getter 메서드를 통해 안전하게 값을 반환하도록 설계되어 있다.

---

### 배열의 두 가지 해결책

배열을 외부에 노출하지 않으려면 아래 두 가지 방법이 있다.

```java
private static final Page[] PAGE_INFO = {...};
public static final List<Page> VALUES = Collections.unmodifiableList(Arrays.asList(PAGE_INFO));
```

-   `불변 리스트를 제공하는 방식`  
    배열을 private으로 선언한 뒤, 이를 기반으로 Collections.unmodifiableList를 사용해 읽기 전용 리스트를 생성하고 외부에 제공한다.  
    이 방식은 배열의 직접적인 수정은 막을 수 있지만, 원본 배열(PAGE\_INFO)이 변경되면 리스트도 영향을 받는다.

```java
public static final Page[] values() {
    return PAGE_INFO.clone();
}
```

-   `복사본을 반환하는 방식`  
    외부에서 배열을 요청할 때, 항상 복사본을 반환하여 원본 배열이 수정되지 않도록 방어한다.  
    이 방법은 원본 배열을 완벽히 보호할 수 있지만, 배열을 매번 복사해야 하므로 성능에 약간의 비용이 발생할 수 있다.