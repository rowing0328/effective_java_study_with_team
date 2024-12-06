## 📖 try-finally 보다는 try-with-resources를 사용하라

- 꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자.

## 💡 주요 내용 정리

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많습니다.

InputStream, OutputStream, java.sql.Connection 등이 좋은 예입니다.

자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어집니다.

이런 자원 중 상당수가 안전망으로 finalizer를 활용하고는 있지만 finalizer는 그리 믿을만하지 못합니다.

꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자.

코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용합니다.

try-finally로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 자원을 회수할 수 있습니다.

## 🛠️ 실습 코드
- 예제 코드 및 결과 코드 설명

## 🤔 토론 및 질문
- 궁금한 점, 논의하고 싶은 내용
- 지금은 finalizer나 cleaner를 사용하지 않고 결국 try-finally, try-with-resources를 사용하기 때문에 해당 기능들을 만날일은 없지 않은지?
- 이번 장은 크게 공감되지가 않아서 이해가 힘들었음 ㅜㅜ
