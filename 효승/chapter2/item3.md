**※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.**

## 아이템 3 - private 생성자나 열거 타입으로 싱글턴임을 보증하라

```java
public class Speaker {

    private static volatile Speaker instance;

    private Speaker() {}

    public static Speaker getInstance() {
        if (instance == null) {
            synchronized (Speaker.class) {
                if (instance == null) {
                    instance = new Speaker();
                }
            }
        }
        return instance;
    }

}
```

-   `지연 초기화(Lazy Initialization)`  
    인스턴스를 필요할 때 처음으로 생성한다.  
    if (instance == null) 조건으로 인스턴스가 없을 때만 생성해 메모리를 효율적으로 관리한다.

-   `synchronized`  
    getInstance() 메서드에 synchronized를 사용해 멀티스레드 환경에서도 안전하게 동작한다.  
    단 하나의 인스턴스만 생성되도록 보장한다.