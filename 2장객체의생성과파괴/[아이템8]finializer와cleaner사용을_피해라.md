java는 두 가지 객체 소멸자를 제공한다. <br> 
<h2>그 중 finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다. </h2> <br> 
자바 9에서 finializer를 deprecated API로 지정하고 cleaner를 그 대안으로 소개했다.<br> 
cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다. <br> <br> 
<br> 
<h2> finalizer와 cleaner는 즉시 수행된다는 보장이 없다. </h2> <br> 
객체에 접근할 수 없게 된 후 finalizer나 cleaner가 실행되기 전까지 얼마나 걸릴지 알 수 없다. <br> 
<b>즉, finalizer와 cleaner 로는 제때 실행되어야 하는 작업은 절대 할 수 없다. </b>
<br> 

<br> 
finalizer나 cleaner를 얼마나 신속하게 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달렸으며, <br> 
이는 가비지 컬렉터 구현마다 천차만별이다. <br> 
finalizer나 cleaner 수행 시점에 의존하는 프로그램의 동작 또한 마찬가지이다. <br> 
<br> 
굼뜬 finalizer 처리는 현업에서도 문제를 일으킨다. <br> 
클래스에 finalizer를 달아두면 그 인스턴스의 자원 회수가 제멋대로 지연될 수 있다. <br> 

<hr> 
<h2> 자바 언어 명세는 finalizer나 clenaner의 수행 시점 뿐 아니라 수행 여부조차 보장하지 않는다. </h2><br> 
접근할 수 없는 일부 객체에 딸린 종료 작업을 전혀 수행하지 못한 채 프로그램이 중단될 수 있다. <br> 
프로그램의 생애주기와 상관없는, 상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안된다. <br> 
System.gc나 System.runFinalization 메서드에 현혹되지 말자. <br> 

* finalizer 동작 중 발생한 예외는 무시되며, 처리한 작업이 남았더라도 그 순간 종료된다. <br>
* 잡지 못한 예외 때문에 해당 객체는 자칫 마무리가 덜 된 상태로 남을 수 있다. <br>

그리고 다른 스레드가 이처럼 훼손된 객체를 사용하려 한다면 어떻게 동작할 지 예측할 수 없다. <br> 
보통의 경우엔 잡지 못한 예외가 스레드를 중단시키고 스택 추적 내역을 출력하겠지만, 같은 일이 finalizer에서 일어난다면 경고조차 출력하지 않는다. <br> 
<br> 
<h2> finalizer와 clenaer는 심각한 성능 문제도 동반한다.</h2> <br> 
finalizer가 가비지 컬렉터의 효율을 떨어트리기 때문이다. <br> 
cleaner도 클래스의 모든 인스턴스를 수거하는 형태로 사용하면 성능은 finalizer와 비슷하다. <br> 
<br> 
<h2>finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다. </h2> <br> 
생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다. <br> 
이 finalizer는 정적 필드에 자신의 참조를 할당하여 가비지 컬렉터가 수집하지 못하게 막을 수 있다. <br> 
이렇게 일그러진 객체가 만들어지고 나면, 이 객체의 메서드를 호출해 애초에는 허용되지 않았을 작업을 수행하는 건 일도 아니다. <br> 
<b> 객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않다. </b> 
<br> 
* => final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언하자. <br> 


<hr>
<h2> AutoClosable을 구현해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 된다. </h2> 
<br> 
각 인스턴스는 자신이 닫혔는지를 추적하는 것이 좋다. <br> 
즉 close 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException을 던지는 것이다. <br> 
<br>  

<h3> cleaner와 finalizer의 쓰임새 </h3> 
1. 자원의 소유자가 close 메소드를 호출하지 않는 것에 대비한 안정망 역할  ex) FileInputStream, FileOutputStream, ThreadPoolExecutor <br> 
2. 네이티브 피어와 연결된 객체에서 <br> <br> 
* 네이티브 피어란 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체를 말한다. <br>  
네이티브 피어는 자바 객체가 아니니 가비지 컬렉터는ㄴ 그 존재를 알지 못한다. <br> 
그 결과 자바 피어를 회수할 때 네이티브 객체까지 회수하지 못한다. <br> 
cleaner나 finalizer가 나서서 처리하기에 적당한 작업이다. <br> 
단, 성능 저하를 감당할 수 있고 네이티브 피어가 심각한 자원을 가지고 있지 않을때만 해당된다. <br> 
