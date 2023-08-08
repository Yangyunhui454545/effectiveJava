equals 메서드는 재정의하기 쉬워 보이지만 곳곳에 함정이 도사리고 있다. <br> 
문제를 회피하는 가장 쉬운 길은 아예 재정의하지 않는 것이다. <br> 

<h3> 아래 사항 중 하나에 해당한다면 재정의하지 않는 것이 최선이다. </h3>  

* 각 인스턴스가 본질적으로 고유하다. 
  * 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기 해당한다.
  * Thread가 좋은 예로, Object의 equals 메서드는 이러한 클래스에 딱 맞게 구현되었다. 
* 인스턴스의 '논리적 동치성을 검사할 일이 없다.' 
  * java.util.regex.Pattern은 equals를 재정의해서 두 Patteren의 인스턴스가 같은 정규표현식을 나타내는 지를 검사하는 방법도 있다. 
* 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
  * Set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, 
  *  List 구현체들은 AbstractList로부터, 
  *  Map 구현체들은 AbstractMap으로부터 상속받아 그대로 쓴다.
* 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.

<br> 

```java 
@Override
public boolean equals(Object o) {
  throw new AssertionError(); //호출 금지!
}
``` 
<br> <hr> 

- equals 재정의가 필요한 경우

객체 식별성 (두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, <br> 
상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때다. <br> 
주로 값 클래스들이 여기 해당된다. <br> 
<br> 
값 클래스란 Integer와 String 처럼 값을 표현하는 클래스를 말한다. <br> 
두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶어할 것이다. <br> 
equals가 논리적 동치성을 확인하도록 재정의해두면, <br> 
그 인스턴스는 값을 비교하길 원하는 프로그래머의 기대에 부응함은 물론 Map의 키와 Set의 원소로 사용할 수 있게 된다. <br> 
<br> 
값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의하지 않아도 된다. (아이템1) <br> 
Enum도 마찬가지이다. (아이템 34) <br> 
클래스에서는 어차피 논리적으로 같은 인스턴스가 2개 이상 만들어지지 않으니 <br> 
논리적 동치성과 객체 식별성이 사실상 똑같은 의미가 된다. <br> 
따라서 Object의 equals가 논리적 동치성까지 확인해준다고 한다. <br> 
<br> 

<hr>
equals 메서드를 재정의할 때는 반드시 일반 규약을 따라야 한다. <br> 

* 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.
* 대칭성(symmetry) : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true면 y.equals(x)도 true이다.
* 추이성(transitivity) : null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true이다.
* 일관성(consistency) : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
* null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false이다.

<hr> 
<br> 

- 동치관계란 : 집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산이다. <br> 
equals메서드가 쓸모 있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 한다. <br> 

<h3> 반사성 </h3>  
단순히 말하면 객체는 자기 자신과 같아야 한다는 뜻이다. <br> 
이 요건은 일부러 어기는 경우가 아니라면 만족시키지 못하기가 더 어려워 보인다. <br> 
이 요건을 어긴 클래스의 인스턴스를 컬렉션에 넣은 다음 contains 메서드를 호출하면 방금 넣은 인스턴스가 없다고 답할 것이다. <br> 
<br> 

<h3> 대칭성 </h3>  
두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다는 뜻이다. <br> 

```java
public final Class CaseInsensitiveString {
  private final String s;

  public CaseInsensitiveString(String s) {
    this.s = Objects.requireNonNull(s);
  }

  // 대칭성 위배 !
  @Override public boolean equals(Object o) {
    if(o instanceof CaseInsensitiveString)
      return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
    if(o instanceOf String) // 한 방향으로만 작동한다!
      return s.equalsIgnoreCase((String) o); 
  }
}
```

CaseInsensitiveString의 equals는 순진하게 일반 문자열과도 비교를 시도한다. <br>  
equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없다. <br> 

```java
@Override public boolean equals(Object o) {
  return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
```

<br> 

<h3> 추이성 </h3>

첫번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, <br> 
첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻이다. <br> 
간단하지만 자칫하면 어기기 쉽다. <br> 
<br> 

```java
public class Point {
  private final int x;
  private final int y;

  public Point(int x, int y) {
    this.x = x;
    this.y = y;
  }
  @Override public boolean equals(Object o) {
    if(!(o instanceof Point)) return false;
    Point p = (Point) o;
    return p.x == x && p.y == y;
  }
...
}
```

point 클래스를 확장한 색상 클래스  <br> 

```java
public class ColorPoint extends Point {
  private final Color color;
  public ColorPoint(int x, int y, Color color) {
    super(x, y);
    this.color = color;
  }
}
```

이대로 둔다면 Point 수현이 상속되어 색상 정보는 무시한 채 비교를 수행한다. <br> 
equals 규약을 어긴 것은 아니지만, 중요한 정보를 놓치게 되니 받아들일 수 없는 상황이다. <br> 
비교대상이 또 다른 ColorPoint이고 위치와 색상이 같을 때만 true를 반환하는 equals를 생각해보자.

```java
  @Override public boolean equals(Object o) {
    	if(!o instanceof ColorPoint)
    		return false;
    	return super.equals(o) && ((ColorPoint) o).color == color;
    }
```

```java
  public static void main(){
      Point p = new Point(1,2);
      ColorPoint cp = new ColorPoint(1,2, Color.RED);
      p.equals(cp);    // true
      cp.equals(p);    // false
    }
```
colorpoint의 equals는 입력 매개변수의 클래스 종류가 다르다며 매번 false만 반환할 것이다. <br> 
<br> 

잘못된 코드 추이성 위배~!! <br> 

```java
    @Override public boolean equals(Obejct o) {
      if(!(o instanceof Point))
        return false;
      if(!(o instanceof ColorPoint))
        return o.equals(this);
      return super.equals(o) && ((ColorPoint) o).color == color;
    }
    public static void main() {
      ColorPoint p1 = new ColorPoint(1,2, Color.RED);
      Point p2 = new Point(1,2);
      ColorPoint p3 = new ColorPoint(1,2, Color.BLUE);
      p1.equals(p2);    // true 
      p2.equals(p3);    // true 
      p1.equals(p3);    // false
    }
```
<br> 
p1.equals(p2);와 p2.equals(p3);는 true를 반환하는데, p1.equals(p3);는 false를 반환해 추이성에 위배된다.
이 방식은 무한 재귀에 빠질 위험도 있다. <br> 
<br>

* 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다. <br>

리스코프 치환 원칙 <br> 
https://blog.itcode.dev/posts/2021/08/15/liskov-subsitution-principle <br> 

