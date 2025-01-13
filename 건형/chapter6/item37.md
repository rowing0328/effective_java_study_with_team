## 📖 Ordinal 인덱싱 대신 EnumMap을 사용하라

### 핵심 내용

배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, 대신 EnumMap을 사용하라

다차원 관계는 EnumMap<..., EnumMap<...>> 으로 표현하라.

"애플리케이션 프로그래머는 Enum.ordinal을 (웬만해서는) 사용하지 말아야 한다"는 일반 원칙의 특수한 사례다

## 💡 주요 내용 정리 & 🛠️ 실습 코드

열거 타입을 키로 사용하도록 설계한 아주 빠른 Map 구현체 -> EnumMap

EnumMap을 사용해 데이터와 열거 타입을 매핑한다.

```java
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle =
        new EnumMap<>(Plant.LifeCycle.class);
for (Plant.LifeCycle lc : Plant.LifeCycle.values())
    plantsByLifeCycle.put(lc, new HashSet<>());
for (Plant p : garden)
    plantsByLifeCycle.get(q.lifeCycle).add(p);
System.out.println(plantsByLifeCycle);
```

안전하지 않은 형변환은 쓰지 않고, 맵의 키인 열거 타입이 그 자체로 출력용 문자열을 제공하니 출력 결과에 직접 레이블을 달 일도 없다.

배열 인덱스를 계산하는 과정에서 오류가 날 가능성도 원천봉쇄된다.


## 🤔 생각 정리
- ??? 단순히 계속 ordinal을 사용하지 말라고 말하고 있지 않은가?

