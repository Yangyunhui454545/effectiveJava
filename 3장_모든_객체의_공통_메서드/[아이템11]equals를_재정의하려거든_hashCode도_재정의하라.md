equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 한다. <br> 
그렇지 않으면 hashCode 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 <br> 
HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제를 일으킬 것이다. <br> 
<br><br> 

* equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다.
  * 단 애플리케이션을 다시 실행한다면 이 값이 달라져도 상관없다.
* equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.
* equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다.
 * 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.   

<h3> hashCode 재정의를 잘못했을 때 크게 문제가 되는 조항은 두번째다. </h3>  
<h3> 즉, 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다. </h3> 
<br> 
equals는 물리적으로 다른 두 객체를 논리적으로는 같다고 할 수 있다. <br> 
하지만 Object의 기본 hashCode 메서드는 이 둘이 전혀 다르다고 판단하여, 규약과 달리 서로 다른 값을 반환한다. <br> 
<br> 

```java
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707,867,5309), ""제니);

m.get(new PhoneNumber(707,867,5309)) // null 반환
```

여기에는 2개의 PhoneNumber 인스턴스가 사용되었다. <br> 
PhoneNumber는 hashCode를 재정의하지 않았기 때문에 논리적 동치인 두 객체가 <br> 
서로 다른 해시코드를 반환하여 두 번째 규약을 지키지 못했다. <br> 
<br> 

* 이 문제는 hashCode 메서드만 작성해주면 해결된다.

```java
// 최악의 (하지만 적법한) hashCode 구현 - 사용 금지!
@Override public int hashCode() { return 42; }
```

이 코드는 동치인 모든 객체에서 똑같은 해시코드를 반환하니 적법하다. <br> 
하지만 끔찍하게도 모든 객체에게 똑같은 값만 내어주므로 모든 객체가 해시테이블의 버킷 하나에 담겨 마치 연결리스트처럼(LinkdList) 작동한다. <br> 
그 결과 평균 수행시간이 O(1)인 해시테이블이 O(n)으로 느려져서, 객체가 많아지면 도저히 쓸 수 없게 된다. <br> 
<br> 
<hr>
좋은 해시 함수라면 서로 다른 인스턴스에 다른 해시코드를 반환한다. <br> 
이것이 바로 hashCode의 세 번째 규약이 요구하는 속성이다. <br> 
이상적인 해시 함수는 주어진 인스턴스들을 32비트 정수 범위에 균일하게 분배해야 한다. <br> 
좋은 hashCode를 작성하는 요령

![image](https://github.com/Yangyunhui454545/effectiveJava/assets/43237961/2de876d1-c595-4044-a6fb-9871b80e1bb1)  
