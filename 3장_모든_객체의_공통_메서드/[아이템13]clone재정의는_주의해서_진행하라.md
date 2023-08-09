Cloneable은 복제해도 되는 클래스임을 명시하는 용도의 믹스인(mixin interface, 아이템 20) 인터페이스지만, <br> 
아쉽게도 의도한 목적을 제대로 이루지 못헀다. <br> 

* 믹스인이란?
* https://jake-seo-dev.tistory.com/30


가장 큰 문제는 clone 메서드가 선언된 곳이 Cloneable이 아닌 Object이고, 그마저도 protected라는 데 있다. <br> 
그래서 Cloneable을 구현하는 것만으로는 외부 객체에서 clone 메서드를 호출할 수 없다. <br> 
하지만 이러한 문제점에도 불구하고 Cloneable 방식은 널리 쓰이고 있다. <br> 
<br> 

메서드 하나 없는 Cloneable 인터페이스는 무슨 일을 할까? <br> 
이 인터페이스는 Object의 protected 메서드인 clone의 동작 방식을 결정한다. <br> 
Cloneable을 구현한 클래스의 인스턴스에서 clone을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환하며, <br> 
그렇지 않은 클래스의 인스턴스에서 호출하면 CloneNotSupportedException을 던진다. <br> 
인터페이스를 구현한다는 것은 일반적으로 해당 클래스가 그 인터페이스에서 정의한 기능을 제공한다고 선언하는 행위이다. <br> 
그런데 Cloneable의 경우에는 상위 클래스에 정의된 protected 메서드의 동작 방식을 변경한 것이다. <br> 
<br> 
실무에서 Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, <br> 
사용자는 당연히 복제가 제대로 이뤄지리라 기대한다. <br> 
