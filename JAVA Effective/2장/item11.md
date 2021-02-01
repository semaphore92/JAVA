# 11. Equals를 재정의하려거든 HashCode도 재정의하라.

* Hashcode를 재정의하지 않으면 Hash를 사용하는 HashMap, HashSet과 같은 컬렉션의 원소로 사용될 때 문제가 발생한다.

HashCode의 규약
-----

* equals 비교에 사용되는 정보가 변경되지 않았다면, 객체의 hashcode 메서드는 몇 번을 호출해도 항상 일관된 값을 반환해야 한다.
* equals 메서드를 통해 두 개의 객체가 같다고 판단했다면 두 객체는 모두 똑같은 Hashcode 값을 반환해야한다.
* eqauls 메서드가 두 개의 객체를 다르다고 판단했다 하더라도, 두 객체의 hashCode가 서로 다른 값을 가질 필요는 없다.

```java
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");
m.get(new PhoneNumber(707, 867, 5309));	//null을 반환
```
위 코드는 3번째 라인을 실행하면 null이 반환된다. PhoneNumber 클래스는 논리적으로는 동치이지만 hashCode()를 재정의하지 않았기 때문에 서로 다른 해시코드를 반환하여 null이 반환한다..

* 이러한 문제를 해결하기 위해 아래처럼 hashCode()를 재정의할 수 있지만 절대 사용해서는 안된다.
``` java
//절대 사용하지 말 것!
@Override
public int hashCode() {
    return 42;
}
```


### HashCode()를 작성하는 요령
 Objects 클래스는 임의의 개수만큼 객체를 받아 해시코드를 계산해주는 정적 메서드인 hash()를 제공합니다. 이 메서드를 활용하면 앞서의 요령대로 구현한 코드와 비슷한 수준의 hashCode() 함수를 단 한 줄로 작성할 수 있다. 하지만 속도는 더 느립니다. 입력 인수를 담기 위한 배열이 만들어지고, 입력 중 기본 타입이 있다면 박싱과 언박싱도 거쳐야 하기 때문입니다. 다음 코드는 Objects.hash()를 이용한 방법이다.
```java
@Override
public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
```

