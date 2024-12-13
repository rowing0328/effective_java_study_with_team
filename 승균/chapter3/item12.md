## **📖item12: toString을 항상 재정의하라**

## **💡핵심 주제**
Object의 기본 toString 메서드는 우리가 작성한 클래스 적절한 문자열 반환 x -> 클래스_이름@16진수로_표시한_해시코드 반환 <br/>
toString 일반 규약에 따라 '간결하면서 사람이 읽기 쉬운 형태의 유익한 정보 반환' <br/>
equals and hashCode 규약 만큼 중요하지 않지만, toString 잘 구현한 클래스는 디버깅하기 쉽다. <br/>
toString 메서드는 println, pringf, assert 구문에 넘길 때, 디버거가 객체를 출력할 때 자동으로 호출<br/>

**실전에서 toString은 그 객체가 가진 주요 정보 모두를 반환하는게 좋음**

**포맷을 명시하든 아니든 의도를 명확히 밝혀야 한다.**
```java
/**
 * XXX-YYY-ZZZZ 형태의 12글자로 구성
 * XXX 지역 코드 , YYY 프리픽스 , ZZZZ 가입자 번호
 */
@Override
public String toString() {
    return String.format("%03d-%03d-%04d", areaCode, prefix, lineNum);
}
```

자바가 이미 완벽한 toString 제공하니 따로 제공하지 않아도됨 -> 특별한 경우가 아니면 정의하는 것 추천 x