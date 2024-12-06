## **item9: try-finally 보다는 try-with-resources를 사용하라**

## **핵심 주제**

**try-finally- 더 이상 자원을 회수하는 최선의 방책이 아니다!**

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
      return br.readLine();  
    } finally {
        br.close();
    }
}
```

**자원이 둘 이상이면 try-finally 방식은 너무 지저분하다!**

```java
import java.io.*;

static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```
문제가 생긴다면 firstLineOfFile 메서드 안의 readLine 메서드가 예외를 던지고, 같은 이유로 close 메서드도 실패

이런 상황의 경우 두 번째 예외가 첫 번째 예외에 관한 정보는 남지 않게 되어, 실제 시스템에서의 디버깅을 어려움

** 반드시 닫아야하는 자원을 뜯하는 클래스를 작성하면 AutoCloseable을 반드시 구현**


**try-with-resources- 자원을 회수하는 최선책!**

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

static String firstLineOfFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
```

**복수의 자원을 처리하는 try-with-resources- 짧고 매혹적이다!**

```java
import java.io.*;

static void copy(String src, String dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new Byte[BUFFER_SIZE];
        int n;
        while((n=in.read(buf)) > 0) out.write(buf, 0, n);
    }
}
```

- 훨씬 코드가 간결하고 문제를 진단하기 좋음
- 발생한 예외들도 보존되어 스택 추적 내역에 suppressed 꼬리표로 출력됨

**try-with-resources를 catch절과 함께 쓰는 모습**
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

static String firstLineOfFile(String path, String defaultVal) {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    } catch (IOException e) {
        return defaultVal;
    }
}
```