# 아이템 18: 상속을 고려해 설계하고 문서화하라 그러지 않았다면 상속을 금지하라
여기서 말하는 상속은 인터페이스로 확장하는 상속이 아닌 클래스가 다른 클래스를 확장하는 구현 상속을 말함.

## **핵심 정리**
메서드로 재정의하면 어떤 일이 일어나는지를 정확히 정리하여 문서로 남겨야 한다. <br/>
상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용)문서로 남겨야 한다.

메서드 주석에 @implSpec 태그를 붙여주면 자바독 도구가 생성해줌.

내부 매커니즘을 문서로 남기는 것만이 상속을 위한 설계의 전부는 아님.
**효율적인 하위 클래스를 큰 어려움 없이 만들 수 있게 하려면 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅(hook)을 잘 선별하여 protected 메서드 형태로 공개해야 할 수도 있다.**

removeRange 메서드 예시)

protected the removeRange(int fromIndex, int toIndex)

fromIndex(포함) ~ toIndex(미포함)까지의 모든 원소를 리스트에서 제거한다. <br/>
toIndex 이후의 원소들은 앞으로 (index만큼씩) 당겨진다. 이 호출로 리스트는 'toIndex - fromIndex' 만큼 짧아진다. (toIndex == fromIndex라면 아무런 효과가 없다.) <br/>
이 때, 이 리스트의 부분리스트에 정의된 clear 연산이 이 메서드를 호출한다.

Implementation Requirements: 이 메서드는 fromIndex에서 시작하는 리스트 반복자를 얻어 모든 원소를 제거할 때까지 ListIterator.next와 ListIterator.remove 반복 호출하도록 구현 <br/>
주의 ListIterator.remove 선형 시간이 걸리면 이 구현의 성능은 제곱의 반 비례한다.

하위 클래스에서 부분 리스트의 clear 메섣르르 고성능으로 쉽게 하기 위해 제공되어있음. 최종사용자는 removeRange 메서드에 관심이 없음.

상속용 클래스를 시험하는 방법은 직접 하위 클래스를 만들어봐야함..?

**상속용으로 설계한 클래스는 배포 전에 반드시 하위 클래스를 만들어 검증해야 한다.**

**상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.**

```java

import java.time.Instant;

public class Super {
    public Super() {
        overrideMe();
    }

    public void overrideMe(){
        
    }
}

public final class Sub extends Super {
    private final Instant instant;

    Sub() {
        instance = Instant.now();
    }
    
    @Override
    public void overrideMe() {
        System.out.println(instant);
    }
    
    public static void main(String[] args) {
        Sub sub = new Sub();
        sub.overrideMe();
    }
}

```
instant 두번 출력 기대 했겠지만 맨 처음 null출력 why? 상위 클래스의 생성자는 하위 클래스의 생성자가 인스턴스 필드를 초기화하기도 전에 overrideMe 호출 <br/>

Cloneable, Serializable 인터페이스는 상속용 설계의 어려움을 한층 더해줌. <br/> 
둘 중 하나라도 구현한 클래스를 상속할 수 있게 설계하는 것은 일반적으로 좋지 않은 생각이다. <- 프로그래머에게 엄청난 부담감을 느끼게 함.<br/>
인터페이스들을 하위 클래스에서 구현하도록 하는 방법이 있다. 

상속용 클래스에 Cloneable, Serializable 구현해야 한다면 **clone과 readObject 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.**

readObject 경우 하위 클래스의 상태가 미처다 역직렬화되기 전에 재정의한 메소드부터 호출하게 된다. <br/>
clone 경우 클래스의 clone 메서드가 복제본의 상태를 (올바른 상태로) 수정하기 전에 재정의한 메서드를 호출한다. <br/>

**클래스를 상속용으로 설계하려면 엄청난 노력이 들고 그 클래스에 안기는 제약도 상담함**



