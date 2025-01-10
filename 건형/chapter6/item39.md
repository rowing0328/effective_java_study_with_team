## 📖 명명 패턴보다 에너테이션을 사용하라

### 핵심 내용

## 💡 주요 내용 정리 & 🛠️ 실습 코드

명명패턴의 단점

1. 오타가 나면 안 된다.
2. 올바른 프로그램 요소에서만 사용되리라 보증할 방법이 없다.
3. 프로그램 요소를 매개변수로 전달할 마땅한 방법이 없다.

### 명명 패턴보다 애너테이션을 사용하자!

마커 에너테이션 타입 선언
```java
import java.lang.annotation.*;

/*
 * 테스트 메서드임을 선언하는 애너테이션이다.
 * 매개변수 없는 정적 메서드 전용이다.
 * */
@Retention(RetentionPolicy)
@Target(ElementType.METHOD)
public @interface Test {}
```

@Retention @Target 과 같은 애너테이션을 메타애너테이션이라 한다.

@Retention(RetentionPolicy.RUNTIME)
-> @Test가 런타임에도 유지되어야 한다는 표시

@Target(ElementType.METHOD)
-> @Test가 반드시 메서드 선언에서만 사용돼야 한다고 알려준다. 따라서 클래스 선언, 필드 선언 등 다른 프로그램 요소에는 달 수 없다.


@Test 와 같은 애너테이션을 "아무 매개변수 없이 단순히 대상에 마킹한다"는 뜻에서 마커 애너체이션이라 한다.
```java
public class Sample {
    @Test public static void m1() {} // 성공해야 한다.
    public static void m2() {}
    @Test public static void m3() { // 실패해야 한다.
        throw new RuntimeException("실패");
    }
    public static void m4() {}
    @Test public void m5() {} // 잘못 사용한 예 : 정적 메서드가 아니다.
    public static void m6() {}
    @Test public static void m7() { // 실패해야 한다.
        throw new RuntimeException("실패");
    }
    public static void m8();
}
```

@Test 에너테이션이 Sample 클래스의 의미에 직접적인 영향을 주지는 않는다.

그저 이 애너테이션에 관심 있는 프로그램에게 추가 정보를 제공할 뿐이다.

매개변수 하나를 받는 애너테이션 타입
```java
import java.lang.annotation.*;

/*
 * 명시한 예외를 던져야만 성송하는 테스트 메서드용 애너테이션 
 * */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Exception {
    Class<? extends Throwale> value();
}

// 매개변수 하나짜리 애너테이션을 사용한 프로그램
public class Sample2 {
    @ExceptionTest(ArithmeticException.class)
    public static void m1() { // 성공해야 한다.
        int i = 0;
                i = i / i;
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m2() { // 실패해야 한다. (다른 예외 발생)
        int[] a = new int[0];
        int i = a[1];
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m3() { } // 실패해야 한다. (예외가 발생하지 않음)
}
```

배열 매개변수를 받는 애너테이션 타입
```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Exception {
    Class<? extends Throwale>[] value();
}

// 배열 매개변수를 받는 애너테이션을 사용하는 코드
@ExceptionTest({
        IndexOutOfBoundsException.class,
        NullPointerException.class })
public static void doublyBad() { // 성공해야 한다.
    List<String> list = new Arraylist<>();
    
    // 자바 API 명세에 따르면 다음 메서드는 IndexOutOfBoundsException이나
    // NullPointerException을 던질 수 있다.
    list.addAll(5,null);
}
```

여러 개의 값을 받는 애너테이션을 다른 방식으로도 만들 수 있다.

배열 매개병수를 사용하는 대신 애너테이션에 @Repeatable 메타애너테이션을 다는 방식이다.

@Repeatable을 단 애너테이션은 하나의 프로그램 요소에 여러 번 달 수 있다.

주의할 점

1. @Repeatable을 단 에너테이션을 반환하는 '컨테이너 애너테이션'을 하나 더 정의하고, @Repeatable에 이 컨테이너 애너테이션의 class 객체를 매개변수로 전달해야 한다.
2. 컨테이너 애너티에션은 내부 애너테이션 타입의 배열을 반환하는 value 메서드를 정의해야 한다.
3. 컨테이너 애너테이션 타입에는 적절한 보존 정책(@Retention)과 적용 대상(@Target)을 명시해야 한다.

반복 가능한 애너테이션 타입
```java
// 반복 가능한 애너테이션
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(ExceptionTestContainer.class)
public @interface ExceptionTest {
    Class <? extends Throwable> value();
}

// 컨테이너 애너테이션
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTestContainer {
    ExceptionTest[] value();
}

// 반복 가능 애너테이션을 두 번 단 코드
@ExceptionTest(IndexOutOfBoundsException.class)
@ExceptionTest(NullPointerException.class)
public static void doublyBad() { 
    //... 
}
```

**애너테이션으로 할 수 있는 일을 명명 패턴으로 처리할 이유는 없다!**


## 추가 강의 내용 정리

### 명명패턴

jpa의 custom method의 경우 또한 postfix가 impl로 고정되어 있다.

### 명명패턴의 문제

- 오타에 취약함

- 올바른 프로그램 요소에서만 사용되라는 법이 없음 (원하지 않는 scan으로 인한 error)

- 프로그램 요소를 매개 변수로 전달할 방법이 없다.

**Annotation은 이를 모두 해결해 준다.**

### annotation의 속성

RetentionPolicy enum
```java
public enum RetenionPolicy {
    SOURCE, // 컴파일러에 의해 무시
    CLASS, // 런타임 시 무시 (컴파일 시에만 체크)
    RUNTIME // 런타임 시에도 확인
}
```

ElementType enum
```java
public enum ElementType {
    TYPE,
    FIELD,
    METHOD,
    PARAMETER,
    CONSTRUCTOR,
    LOCAL_VARIABLE,
    ANNOTATION_TYPE,
    PACKAGE,
    TYPE_PARAMETER,
    TYPE_USE,
    MODULE
}
```


명명패턴과 마찬가지로, 일반 프로그래머가 타입을 직접 정의할 일은 거의 없다.
(있다면 확장성을 고려한 marker annotation 정도?)

하지만 자바 프로그래머라면 annotation type의 동작에 대해 이해하고 있어야 하고,

이를 잘 활용하면 코드 품질이 여러모로 좋아 질 수 있다.


## 🤔 생각 정리
- annoation의 생성 방법 및 다양한 사용 법을 배울 수 있었다.
