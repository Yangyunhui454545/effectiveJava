자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. <br> 
InputStream, OutputStream, java.sql.Connection등이 예이다. <br> 
전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally가 쓰였다. <br> 
<br> 
```java
static String firstLineOfFile(String path) throws IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    return br.readLine();
  } finally {
    br.close
  }
}
```

<br> 
예외는 try블록과 finally 블록 모두에서 발생할 수 있는데, readLine 메서드가 예외를 던지고, 같은 이유로 close 메서드도 실패할 것이다. <br> 
이 상황에서는 두 번째 예외가 첫 번째 예외를 집어삼켜 버린다. <br> 
그러면 스택 추적 내역에 첫 번째 예외에 관한 정보는 남지 않게 되어, 기버깅을 어렵게 한다. <br> 
(일반적으로 문제를 진단하려면 처음 발생한 예외를 보고 싶을 것이다.) <br> 
물론 두 번째 예외 대신 첫 번째 예외를 기록하도록 코드를 수정할 수는 있지만, 코드가 너무 지저분해져서 실제로 그렇게까지 하는 경우는 없다. <br> 

<hr> 
이러한 문제들은 try-with-resources 덕에 해결되었다. <br> 
이 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현했다. <br> 
단순히 void를 반환하는 close 메서드 하나만 덩그러니 정의한 인터페이스다. <br> 
자바 라이브러리와 서드파티 라이브러리들의 수많은 클래스와 인터페이스가 이미 AutoCloseable을 구현하거나 확장해뒀다. <br> 
<br> 

```java 
static String firstLineOfFile(String path) throws IOException {
  try(BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine();
  }
}
```
위의 코드애 try with resource를 적용한 모습. <br> 
try-with-resources 버전이 짧고 읽기 수월할 뿐 아니라 문제를 진단하기도 훨씬 좋다. <br> 
readLine과 close 호출 양쪽에서 발생하면, close에서 발생한 예외는 숨겨지고 readLine에서 발생한 예외가 기록된다. <br> 
실전에서는 프로그래머에게 보여줄 예외 하나만 보존되고 여러 개의 다른 예외가 숨겨질 수도 있다. <br> 
<br> 

보통 try-finally에서처럼 try-with-resources에서도 catch 절을 쓸 수 있다. <br> 
catch절 덕분에 try 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있다. <br> 

```java
static String firstLineOfFile(String path, String defaultVal) {
  try(BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine();
  } catch(IOException e) {
    return defaultVal;
  }
}
```

<br> 
* 핵심 정리 <br> 
꼭 회수해야 하는 자원을 다룰 때는 try-finall 말고, try-with-resources를 사용하자. <br>
코드는 더 짧고 분명해지고, 만들어진 예외 정보도 훨씬 유용하다. <br> 
try-finally로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 회수할 수 있다. <br> 
