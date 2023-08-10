이따금 인스턴스 필드들을 모아놓는 일 외에는 아무 목적도 없는 퇴보한 클래스를 작성하려 할 때가 있다. <br> 

```java
class Point {
  public double x;
  public double y;
}

```

이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다. <br> 
API를 수정하지 않고는 내부 표현을 바꿀 수 없고, 불변식을 보장할 수 없으며, <br> 
외부에서 필드에 접근할 때 부수 작업을 수행할 수도 없다. <br>  
철저한 객체 지향 프로그래머는 이런 클래스를 상당히 싫어해서 필드들을 모두 private으로 바꾸고 public 접근자를 추가한다. ,Br> 

```java
class Point {
  private double x;
  private double y;

  public Point (double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double getX() { return x; }
  public double getY() { return y; }

  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}
```

패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 <br> 
클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다. <br>  
<br> 
하지만 package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다. <br> 
그 클래스가 표현하려는 추상 개념만 올바르게 표현해주면 도니다. <br> 

<h2> 핵심 정리 </h2>
public 클래스는 절대 가변 필드를 직접 노출해서는 안된다. <br> 
불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다. <br> 
하지만 package-private 클래스나 private 중첩 클래스에서는 종종 필드를 노출하는 편이 나을 때도 있다. <br> 
<br> 
