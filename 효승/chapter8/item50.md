## 아이템 50 - 적시에 방어적 복사본을 만들라

### 방어적 복사의 필요성

Java의 Date 객체는 설계상의 문제로 인해 원본이 쉽게 변조될 가능성이 있다.

따라서 필요할 때 방어적 복사를 통해 데이터를 보호하는 것이 중요하다.

그렇지 않으면 코드의 예측 가능한 동작이 깨질 수 있다.

예제 코드 - Date 객체의 문제점

```
public Date getNearbyMonth(Date time, int month) {

    if (time.getMonth() > month) {
        time.setYear(time.getYear() + 1);  // 원본이 변경됨
    }
    
    time.setMonth(month);
    return time;
    
}
```

-   위 코드처럼 Date 객체를 직접 조작하는 경우, 원본 객체가 의도치 않게 변경될 수 있다.
-    이런 상황을 방지하기 위해 방어적 복사를 적용하는 것이 좋다.

### 방어적 복사의 적용 방법

방어적 복사는 메소드가 매개변수로 받은 객체나 반환하는 객체를 새로운 복사본으로 만들어 원본이 손상되지 않도록 한다.

예를 들어 getNearbyMonth() 메소드에서 방어적 복사를 적용하려면,

Date 객체의 복사본을 생성하여 해당 복사본을 조작하는 방식으로 변경해야 한다.

### 실무에서의 적용과 주의사항

무조건적으로 복사를 적용하는 것보다 상황에 맞춰 적절히 사용하는 것이 중요하다.

테스트 코드를 통해 방어적 복사의 필요성을 검증하고, 원본 손상 여부를 사전에 확인하는 것도 좋은 방법이다.