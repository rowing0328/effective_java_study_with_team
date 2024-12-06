## **item4: 인스턴스화를 막으려거든 private 생성자를 사용해라**

## **핵심 주제**
1. 정적 멤버만 담은 유틸클래스는 인스턴스로 만들어 쓰려고 만든게 아니다. 
   - 하지만 자동으로 기본생성자를 만들어주어서 가끔씩 의도치않게 유틸클래스를 생성해주는 경우가 있음. << 실제로 제가 이랬음 ㅠ
   - 추상 클래스를 만드는 것으로 인스턴스화 못막음. 오히려 상속해서 쓰라는거 같음
   - 결론 private로 기본생성자를 만들자.

```java
public  class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
}
```
