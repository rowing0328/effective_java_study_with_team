## 아이템 48 - 스트림 병렬화는 주의해서 적용하라

병렬 스트림(paraller stream)은 여러 코어를 활용하여 작업을 병렬적으로 처리할 수 있게 설계된 기능이지만,

모든 상황에서 성능을 보장하거나 효율적이지 않다.

### 병렬 스트림의 제한점

단순히 빠르다고 생각하지 말자

병렬 스트림이라고 무조건 단일 스트림보다 빠른 것은 아니다.

병렬 처리에는 스레드 간 통신과 작업 분배, 병합 비용이 추가로 발생한다.

특히, 데이터 양이 적거나, 연산이 간단한 경우 병렬 처리가 오히려 단일 스트림보다 느릴 수 있다.

데이터 분배와 처리 균형

병렬 처리 시 데이터가 균등하게 나뉘지 않으면, 특정 스레드가 과도하게 부하를 받게 된다.

이는 병렬 처리의 효율성을 낮추는 주요 원인이다.

외부 API 호출 시 주의

병렬 스트림 내부에서 외부 시스템 호출(예: 네트워크 I/O)을 사용하는 경우,

예상치 못한 지연이나 병목현상이 발생할 수 있다.

데이터 구조의 선택이 중요

ArrayList와 같은 데이터 구조는 병렬 처리에서 좋은 성능을 내지만, LinkedList는 그렇지 않다.

데이터 구조의 특성을 고려하여 병렬 스트림을 적용해야 한다.

### ForkJoinPool과 병렬 스트림

병렬 스트림은 기본적으로 ForkJoinPool을 사용하여 작업을 병렬 처리한다.

ForkJoinPool은 divide-and-conquer 방식으로 작업을 분할하고 병합하여 병렬 작업을 수행한다.

그러나 ForkJoinPool을 과도하게 활용하면 전반적인 시스템 성능에 영향을 줄 수 있다.

ForkJoinPool 커스터마이징

기본 ForkJoinPool을 커스터마이징하는 방법도 존재하지만, 추천되지 않는다.

ForkJoinPool의 동작을 변경하면 예기치 못한 부작용(Side Effects)을 유발할 수 있다.

예를 들어, 시스템 전체의 병렬 작업 성능이 저하될 수 있다.

### 병렬 스트림 사용 시 체크리스트

-   측정과 검증 필수  
    병렬 스트림이 실제로 성능을 향상시키는지 반드시 벤치마킹하고 검증해야 한다.
-   병렬 작업에 적합한 작업인지 점검  
    병렬 작업에 적합하지 않은 경우 오히려 성능 저하를 초래할 수 있다.  
    예를 들어, 정렬, 그룹화와 같은 작업은 단일 스트림이 더 효율적일 수 있다.
-   적합한 데이터 구조 선택  
    병렬 스트림을 활용한 데이터 구조(ArrayList vs LinkedList)을 신중히 선택한다.
-   작업의 비대칭성  
    작업 분할이 비대칭적인 경우 병렬 스트림은 기대한 성능을 내지 못할 수 있다.
-   Side Effect 없는 코드 작성  
    병렬 스트림 내부에서 외부 상태를 수정하거나 의존하는 코드는 피해야 한다.

병렬 스트림은 강력한 도구지만,

잘못 사용하면 오히려 성능을 저하시킬 수 있다.

병렬 스트림을 적용하기 전,

위의 체크리스트를 참고하여 신중히 결정한다.

예제 코드

```java
public Integer parallelExpSum(List<Integer> inputList, int minValue) {
    return inputList.stream()
                    .parallel()
                    .filter(input -> input >= minValue)
                    .mapToInt(input -> input * input)
                    .sum();
}

// ForkJoinPool을 활용한 병렬 처리
public Integer parallelExpSumCustomPool(List<Integer> inputList, int minValue) throws ExecutionException, InterruptedException {
    ForkJoinPool forkJoinPool = new ForkJoinPool(5);
    return forkJoinPool.submit(() ->
            inputList.stream()
                     .parallel()
                     .filter(input -> input >= minValue)
                     .mapToInt(input -> input * input)
                     .sum()
    ).get();
}
```
