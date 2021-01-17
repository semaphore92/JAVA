1. 생성자 대신 정적 팩토리 메서드를 고려하라.
====================



정적 팩토리 메서드란 무엇인가?
-------------------------------

객체 생성을 캡슐화 및 객체를 생성하느 메소드를 만들고 static으로 선언하는 기법.

ex) 자바의 valueOf 메서드가 대표적인 예
```java
BigInteger answer = BigInteger.valueOf(42L);
```
static으로 선언된 메서드이며, new BigInteger(...) 은닉하고 있다.



정적 팩토리 메서드의 장,단점
-------------------------------

### 장점

* 이름을 가질 수 있기 때문에 메서드의 정확한 목적을 알기 쉬다

* 호출 시 새로운 색체를 생성할 필요가 없다

* 하위 타입 객체를 반환할 수 있다.



### 단점

* 정적 팩토리 메서드만 있는 클래스라면 하위 클래스를 못 만든다
* 정적 팩토리 메서드의 설명을 찾기가 힘들다.



#### 메서드의 정확한 목적을 알기가 쉽다.

```java
public class Character {
    int intelligence, strength, hitPoint, magicPoint;

    public Character(int intelligence, int strength, int hitPoint, int magicPoint) {
        this.intelligence = intelligence;   // 지능
        this.strength = strength;           // 힘
        this.hitPoint = hitPoint;           // HP
        this.magicPoint = magicPoint;       // MP
    }

    // 정적 팩토리 메소드
    public static Character newWarrior() {
        return new Character(5, 15, 20, 3);     // 전사는 힘과 HP가 높다
    }

    // 정적 팩토리 메소드
    public static Character newMage() {
        return new Character(15, 5, 10, 15);    // 마법사는 지능과 MP가 높다
    }
}

```

####생성자를 사용하여 캐릭터를 생성한다면 다음과 같을 것이다.

```java
Character warrior = new Character(5, 15, 20, 3);
Character mage = new Character(15, 5, 10, 15);
```
변수명이 없기 때문에 숫자값만으로는 메서드의 목적을 알기 어렵다.

정적 팩토리 메서드를 사용하면 읽기 쉬운 코드가 된다.
```java
Character warrior = Character.newWarrior();
Character mage = Character.newMage();
```


#### 호출 시 새로운 객체를 생성할 필요가 없다.

1번 항목과 같은 코드는 정적 팩토리 메서드를 호출 시 new Character를 호출한다.
그러나 immutable 객체를 캐시해서 쓴다면 굳이 new 연산자를 사용할 필요가 없다.
```java
public static final BigInteger ZERO = new BigInteger(new int[0], 0);

private final static int MAX_CONSTANT = 16;
private static BigInteger posConst[] = new BigInteger[MAX_CONSTANT+1];
private static BigInteger negConst[] = new BigInteger[MAX_CONSTANT+1];

static {
    /* posConst에 1 ~ 16까지의 BigInteger 값을 담는다. */
    /* negConst에 -1 ~ -16까지의 BigInteger 값을 담는다. */
}

public static BigInteger valueOf(long val) {
    // 미리 만들어둔 객체를 리턴한다
    if (val == 0)
        return ZERO;
    if (val > 0 && val <= MAX_CONSTANT)
        return posConst[(int) val];
    else if (val < 0 && val >= -MAX_CONSTANT)
        return negConst[(int) -val];

    // 새로운 객체를 만들어 리턴한다
    return new BigInteger(val);
}

```

#### 하위 자료형 객체를 반환할 수 있다
리턴하는 객체의 타이을 유연하게 지정할 수 있다.

```java
class OrderUtil {

    public static Discount createDiscountItem(String discountCode) throws Exception {
        if(!isValidCode(discountCode)) {
            throw new Exception("잘못된 할인 코드");
        }
        // 쿠폰 코드인가? 포인트 코드인가?
        if(isUsableCoupon(discountCode)) {
            return new Coupon(1000);
        } else if(isUsablePoint(discountCode)) {
            return new Point(500);
        }
        throw new Exception("이미 사용한 코드");
    }
}

class Coupon extends Discount { }
class Point extends Discount { }
```
할인 코드의 규칙에 따라 Coupon과 Point 객체를 선택적으로 리턴한다.
이와 같은 방식을 위해선 두 하위 클래스가 같은 인터페이스를 구현하거나 같은 부모클래스를 갖고있어야 한다.


참고 문헌
https://johngrib.github.io/wiki/static-factory-method-pattern/
