# item16:public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

## **핵심정리**

**이처럼 퇴보한 클래스는 public X**
````java
class Point {
    public double x;
    public double y;
}
````

데이터 필드에 접근할 수 있어 캡슐화 이점 제공 x <br/>
API 수정하지 않고는 내부 표현 바꿀 수 없음, 불변식을 보장할 수도 없다. 

**접근자와 변경자(mutator)메서드를 활용해 데이터를 캡슐화**
```java
class Point {
    private double x;
    private double y;
    
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double getX() { return x;}
    public double getY() { return y;}
    
    public void setX(double x) { this.x = x;}
    public void setY(double y) { this.y = y;}
}
```
**패키지 밖에서 접근할 수 있는 접근제어자 제공**

private 중첩 클래스라면 데이터 필드를 노출한다고 해도 문제 없음, 

왜 가능하지?
```java
package mypackage;

public class Parent {
    private static class Data {
        int value; // 필드를 직접 노출
    }

    public void process() {
        Data data = new Data();
        data.value = 42; // 직접 접근
    }
}
```
해당 코드처럼 클래스 내부에서만 필드 값이 사용되기 때문에 괜춘 

**불변 필드를 노출한 public 클래스 - 과연 좋은가?**
```java
public final class Time {
    private static final int HOURS_PER_DAY = 24;
    private static final int MINUTES_PER_HOURS = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if(hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("시간: " + hour);
        if(minute < 0 || minute >= MINUTES_PER_HOURS)
            throw new IllegalArgumentException("분: "+ minute);
        this.hour = hour;
        this.minute = minute;
    }
}
```

