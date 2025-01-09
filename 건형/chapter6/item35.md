## 📖 ordinal 메서드 대신 인스턴스 필드를 사용하라

### 핵심 내용

## 💡 주요 내용 정리 & 🛠️ 실습 코드

대부분의 열거 타입 상수는 자연스럽게 하나의 정숫값에 대응된다.

모든 열거 타입은 해당 상수가 그 열거 타입에서 몇 번째 위치인지를 반환하는 ordinal 이라는 메서드를 제공한다.

이런 이유로 열거 타입 상수와 연결된 정숫값이 필요하면 ordinal 메서드를 이용하고 싶은 유혹에 빠진다.

ordinal을 잘못 사용한 예 - 따라 하지 말 것!
```java
public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET,
    SEXTET, SEPTET, OCTET, NONET, DECTET;
    
    public int numberOfMusicians() { return ordinal() + 1; }
}
```

동작은 하지만 유지보수하기가 끔찍하다.

상수 선언 순서를 바꾸는 순간 오작동하며, 이미 사용 중인 정수와 값이 같은 상수는 추가할 방법이 없다.

또한, 값을 중간에 비워둘 수도 없다.

### 해결책

**열거 타입 상수에 연결된 값은 ordinal 메서드로 얻지 말고, 인스턴스 필드에 저장하자!**

```java
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5),
    SEXTET(6), SEPTET(7), OCTET(8), DOUBLE_QUARTET(8),
    NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;
    Ensemble(int size) {
        this.numberOfMusicians = size;
    }
    public int numberOfMusicians() { return numberOfMusicians; }
}
```


## 추가 강의 내용 정리

Enum에선 ordinal() 이라는 enum의 번호를 반환해 주는 메서드가 있다. (실제로 쓸일은 잘 없음)

하지만 경우에 따라 넘버링을 bit로 하는 경우도 있고 (1,2,4) 코드를 잘못 건드리는 순간 겉잡을 수 없을 수도 있다.

그러니 인스턴트 필드에 값을 저장해서 쓰자.

Ordinal의 용도는 EnumSet, EnumMap과 같이 열거 타입 기반의 자료구조를 위함이다.


## 🤔 생각 정리
- Ordinal을 사용할 일은 없을 것이다!

