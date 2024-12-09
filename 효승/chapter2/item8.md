## 아이템 8 - finalizer, cleaner를 피하라

```java
public class Item8 {

    @Override
    protected void finalize() {
        System.out.println("call finalize");
    }
    
}

public class MainApplication {
    
    private void run() {
        Item8 item8 = new Item8();
    }

    public static void main(String[] args) {
        MainApplication mainApplication = new MainApplication();
        mainApplication.run();
        System.gc(); // GC를 강제로 트리거 (힌트 제공)
    }
    
}
```

-   `Finalizer의 문제점`  
    -   예측 불가능 : GC 실행 시점을 알 수 없어, 정리 작업이 지연될 수 있다.
    -   성능 저하 : GC 성능에 영향을 미쳐 시스템 효율성을 떨어뜨린다.
    -   안전성 문제 : finalize()에서 예외 발생 시 자원 누수가 발생할 수 있다.

-   `System.gc()의 한계`  
    -   강제성이 없음 : JVM에 GC를 실행하라는 힌트일 뿐이며, 실제 실행 여부는 JVM의 구현과 상황에 따라 달라진다.
    -   비권장 : GC 타이밍은 JVM이 최적화하도록 맡겨야하며, System.gc()에 의존하는 것은 비효율적이다.

<br>

```java
import java.lang.ref.Cleaner;

public class CleanObject implements AutoCloseable {
    
    private static final Cleaner cleaner = Cleaner.create();

    private static class CleanData implements Runnable {
        @Override
        public void run() {
            System.out.println("Cleaning resources...");
        }
    }

    private final CleanData cleanData;
    private final Cleaner.Cleanable cleanable;

    public CleanObject() {
        this.cleanData = new CleanData();
        this.cleanable = cleaner.register(this, cleanData);
    }

    @Override
    public void close() {
        cleanable.clean(); // 명시적으로 자원 정리
        System.out.println("CleanObject closed.");
    }
    
}
```

-   `GC 의존성`   
    Cleaner는 GC에 의존적으로 동작하므로 자원의 해제가 GC 실행 시점에 좌우된다.

-   `즉시성 부족`  
    Cleaner는 비동기적으로 실행되기 때문에 정리 작업이 즉시 이루어질 것이라는 보장이 없다.