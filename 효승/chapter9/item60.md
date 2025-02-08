## 아이템 60 - 정확한 답이 필요하다면 float와 double은 피하라

### 부동소수점 연산의 문제점

float와 double은 이진 부동소수점 연산을 사용하기 때문에, 정확한 값이 필요한 계산에서 오차가 발생할 수 있다.

특히 소수점 이하 값을 다루는 금융 계산에서 이러한 오차는 큰 문제를 초래할 수 있다.

예제 코드

```java
double result = 1.0 - 0.10 - 0.20 - 0.30;
System.out.println(result);  // 출력: 0.39999999999999997
```

-   이처럼 계산 결과가 예상과 다르게 출력되는 이유는 이진 부동소수점이 일부 실수를 정확히 표현하지 못하기 때문이다.

해결 방법

정확한 계산이 필요한 경우에는 다음 두 가지 방법을 고려할 수 있다.

1\. BigDecimal 사용

```java
BigDecimal value = new BigDecimal("1.0")
                          .subtract(new BigDecimal("0.10"))
                          .subtract(new BigDecimal("0.20"))
                          .subtract(new BigDecimal("0.30"));
System.out.println(value);  // 출력: 0.40
```

2\. 정수 타입 사용

```java
public class MoneyCalculation {
    public static void main(String[] args) {
        // 소수 대신 센트 단위로 변환하여 계산
        int price1 = 1000;    // 10.00달러 -> 1000센트
        int price2 = 250;     // 2.50달러 -> 250센트
        int price3 = 175;     // 1.75달러 -> 175센트

        // 총합 계산 (정수로 수행)
        int totalAmount = price1 + price2 + price3;

        // 다시 소수로 변환하여 출력
        System.out.printf("Total amount: $%.2f%n", totalAmount / 100.0);
        
        // Total amount: $14.25
    }
}
```