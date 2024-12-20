※ 책 내용을 바탕으로 제 관점에서 풀어 쓴 글입니다. 일부 내용이 다를 수 있습니다.

## 아이템 14 - Comparable을 구현할지 고려하라

```
public class Person {
	private int age;
    private String name;
    private double height;
}
```

위 코드는 나이, 키, 이름 순으로 정렬하는 예제를 위한 간단한 Person 클래스이다.

---

### compareTo 메서드 구현

```
public int compareTo(Person p) {
	int result = Integer.compare(age, p.age);
    
    if (result == 0) {
    	result = Double.compare(height, p.height);
    }
    
    if (result == 0) {
    	result = name.compareTo(p.name);
	}
    
    return result;
}
```

위 코드는 Comparable 인터페이스를 구현하여 Person 객체의 정렬 기준을 정의한다.

---

### Comparator를 사용한 또 다른 구현

```
private static final Comparator<Person> COMPARATOR = Comparator.comparingInt(Person::getAge)
		.thenComparingDouble(Person::getHeight)
        .thenComparing(person -> person.getName()
);

public int compareTo(Person p) {
    return COMPARATOR.compare(this, p);
}
```

위 코드는 Comparator를 활용하여 Person 객체의 정렬 기준을 정의하고, Comparable 인터페이스의 compareTo 메서드에서 이를 간단히 호출하는 방식이다.

---

### compareTo의 규약

-   `x.compareTo(y) > 0`  
    x가 y보다 크다는 의미이다.
-   `x.compareTo(y) == 0`  
    x와 y가 같음을 의미한다.
-   `x.compareTo(y) < 0`  
    x가 y보다 작다는 의미이다.
-   `비교 할 수 없는 경우`  
    ClassCastException이 발생한다.