## **item8: finalizer와 cleaner 사용을 피하라**

## **핵심 주제**

1. finalizer 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다. => 해당은 자바18버전에서 삭제됨
   - finalizer와 cleaner는 즉시 수행된다는 보장이 없다.
    - **finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없다.**
2. finalizer와 cleaner 심각한 성능 문제도 동반
3. finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킴
4. 객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만,finalizer가 있다면 그렇지도 않다.
5. final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언

**cleaner를 안정망으로 활용하는 AutoCloseable 클래스**
```java
import java.lang.ref.Cleaner;

public class Room implements AutoCloseable {
    private static final Cleaner cleaner = Cleaner.create();
    
    private static class State implements Runnable {
        int numJunkPiles;
        
        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }
        
        @Override
        public void run() {
            System.out.println("방청소");
            numJunkPiles = 0;
        }
    }
    
    private final State state;
    
    private final Cleaner.Cleanable cleanable;
    
    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }
    
    @Override
    public void close() {
        cleanable.clean();
    }
    
}
```

static으로 선언된 중첩 클래스인 State는 cleaner가 방을 청소할 때 수거할 자원들을 담고 있다.

외부 클래스와 내부 클래스 State 서로 참조하는 구조가 이루어진다면 객체는 계속 참조되기때문에 메모리에 계속 축적되어 가비지 컬렉터가 가지고 갈 수 없다.

그래서 위에 구조처럼 한쪽만 참조하여 메모리를 회수 할 수 있도록 만들어야한다.