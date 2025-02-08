## 아이템 63 - 문자열 연결은 느리니 주의하라

### 문자열 연결의 성능 문제

Java의 문자열은 불변(Immutable) 특성을 가지기 때문에, 문자열을 연결할 때마다 새로운 문자열 객체가 생성된다.

이로 인해 문자열을 반복해서 연결하면 성능이 급격히 저하될 수 있다.

특히 연결 횟수가 많아질수록 시간 복잡도가 n제곱에 가까워지며, 메모리 사용량도 증가하게 된다.

이러한 문제를 해결하기 위해서는 StringBuilder와 같은 가변 문자열 클래스를 활용하는 것이 좋다.

예제 코드 (비효율적인 문자열 연결)

```
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // 매번 새로운 문자열 객체 생성
}
```

해결 방법

StringBuilder는 내부적으로 가변 배열을 사용해 문자열을 효율적으로 연결한다.

개선된 코드

```
StringBuilder result = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    result.append(i);
}
System.out.println(result.toString());
```

-   이 방법은 문자열 연결 시 매번 객체를 생성하지 않기 때문에 성능이 크게 향상된다.