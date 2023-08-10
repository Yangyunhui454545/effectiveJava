Comparable 인터페이스의 유일무이한 메서드인 compareTo <br> 
다른 메서드들과 달리 compareTo는 Object의 메서드가 아니다. <br> 
성격은 Object의 equals와 비슷하다. <br> 
compareTo는 단순 동치성 비교에 더해 순서까지 비교할 수 있으며, 제너릭하다. <br> 
Comparable을 구현했다는 것은 그 클래스의 인스턴스들에는 순서가 있음을 뜻한다. <br> 
<br> 

```java
public interface Comparable<T> {
  int compareTo(T t);
}
```

compareTo의 규약 <br> 
이 객체와 주어진 객체의 순서를 비교한다. <br> 
이 객체가 주어진 객체보다 작으면 음의 정수를, 같으면 0을, 크면 양의 정수를 반환한다. <br> 
이 객체와 비교할 수 없는 타입의 객체가 주어지면 ClassCastException을 던진다. <br> 
<br> 

- Comparable을 구현한 클래스는 모든 x, y에 대해 sgn(x.compareTo(y)) == -sgn(y.compareTo(x)) 여야 한다.
- Comparable을 구현한 클래스는 추이성을 보장해야 한다.
- Comparable을 구현한 클래스는 모든 z에 대해 x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareRo(z))다.
- (x.compareTo(y) == 0) == (x.equals(y))를 지키는 게 좋다.

이 클래스의 순서는 equals 메서드와 일관되지 않다. <br>
<hr> 
  
compareTo 메서드는 각 필드가 동치인지를 비교하는 게 아니라 그 순서를 비교한다. <br> 
객체 참조 필드를 비교하려면 compareTo 메서드를 재귀적으로 호출한다. <br> 
Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 비교자를 대신 사용한다. <br> 
비교자는 직접 만들거나 자바가 제공하는 것 중에 골라 쓰면 된다. <br> 

<br> <br> 
<hr>
<h3> 정리 </h3>
순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여, <br> 
그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 한다. <br> 
compareTo 메서드의 필드의 값을 비교할 때는  <와> 연산자는 쓰지말아야 한다. <br> 
그 대신 박싱된 기본 타입 클래스가 제공하는 compare 메서드나 Comparatroe 인터페이스가 제공하는 비교자 생성 메서드를 사용하자. <br> 

```java
//정적 compare 메서드를 활용한 비교자
static Comparator<Object> hashCodeOrder = new Comparator<>() {
  public int compare(Object o1, Object o2) {
    return Integer.compare(o1.hashCode(), o2.hashCode());
  }
};
```

```java
// 비교자 생성 메서드를 활용한 비교자
static Comparator<Object> hashCodeOrder = Comparator.comparingInt( o-> o.hashCode());
```

