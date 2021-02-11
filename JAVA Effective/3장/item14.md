# 14. Comparable을 구현할지 고려하라

* Comparable을 구한하면 compareTo를 재정의하여 손쉽게 컬렉션을 정렬 할 수 있다.

```java
Arrays.sort(arr);
```
* 알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면, 반드시 Comparable 인터페이스를 구련하자

Comparable은 compareTo만 가지고 있어 Functional Interface로 착각할 수 있지만 그렇지 않다.


## compareTo 메서드 규약

### 대칭성
* Comparable을 구현한 클래스는 모든 x,y에 대해 x.compareTo(y) == (y.compareTo(x))*(-1)을 만족해야 한다.
* x.compareTo(y)가 예외를 던지는 경우, y.comparaeTo(x)도 예외를 던져야 한다.


### 추이성
* Comparable을 구현한 클래스는 모든 x,y,z,에 대해 x.compareTo(y) > 0 이고, y.compareTo(z)이면, x.comparaeTo(z)를 만족해야 한다.

### 반사성
* Comparable을 구현한 클래스 z는 x.comparaeTo(y) == 0이면, sgn(x.comparaeTo(z)) == sgn(y.comparaeTo(z))를 만족해야한다.

## equals와 compareTo 차이점
compareTo 규약 4번째의 예시로 BigDecimal 클래스가 있다.  
new Decimal("1.0")과 new Decimal("1.00")이 있다고 할때 두 객체를 HashSet<Decimal>에 담게 되면 size는 2개가 된다.
하지만 TreeSet<Decimal>에 담게 되면 size는 1개가 된다.

HashSet에서는 equals를 기반으로 비교하기 때문에 new Decimal("1.0")과 new Decimal("1.00")은 서로 다른 객체이다.  
그렇기 때문에 size는 2개가 된다.  

* 하지만 TreeSet에서는 객체에 대한 동치성ㅇ 비교를 compareTo로 하기 때문에 0을 리턴한다.


## CompareTo() 메서드의 구현 패턴

관계 연산자 "<",">"를 사용하는 것은 거추장스럽고 오류를 유발하니 피해야한다.

* 잘못된 예시
```java
public int compareTo(int x, int y){
	return (x<y) ? -1 : ((x ==y) ? 0:1);
}
```
* 올바른 예시
```java
public int compareTo (int x, int y){
	return Integer.compare(x,y);
}
```


