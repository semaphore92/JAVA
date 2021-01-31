# 10. equals는 일반규약을 지켜 재정의하라.

* equals는 메서드는 Object의 정보들에 대해 동등성을 비교하는 목적으로 사용한다.
* equals는 메서드는 잘못 작성하면 잘못된 결과를 만들 수 있다.

equals를 재정의하지 않는 경우
-----
* 각 인스턴스가 본질적으로 고유하다.  주로 값을 표현하는게 아니라 동작하는 것을 표현하는 클래스가 여기에 해당

* 인스턴스의 논리적 동치성을 확인할 필요가 없다.

* 상위 클래스에서 재정의한 equals가 하위 클래스에서도 같은 상황이다. 

* 클래스가 private이거나, package-private이고 equal를 상속받아 쓰는 경우가 존재한다.

재정의 해야할 때
-----
equals를 재정의 해야할 때가 있을까? 바로 다음과 같을 때이다.

* equals 메소드는 ‘메모리주소를 기반으로 물리적으로 같은가?’  즉 객체의 식별성(object identity) 가 아니라 논리적 동치성(logical equality)를 비교해야할 때 재정의하면 좋다.  주로 값 클래스들에 해당된다. 다시 말하자면 객체가 같은지가 중요한게 아니라, 객체 내 값이 같은지 비교해야할 때 재정의해야한다.  (Map의 키, Set의 원소 등으로 사용하려면 재정의해야한다.)


equals 메서드 재정의 시 지켜야 할 다섯가지 규약
-----

1. 반사성(reflexivity)  
null이 아닌 모든 참조 값 x에 대해 x.equals(x)가 true면 y.equals(x)도 true이다.



```java
public final class CaseInsensitiveString(){
    private final String s;

    public CaseInsensitiveString(String s){
        this.s = Objects.requireNonNull(s);
    }
    
    //equalsIgnoreCase은 대소문자 구별없이 문자열 equals 검사
    @Override public boolean equals(Object o) {
        if (o instance of CaseInsensitiveString)
            return s.equalsIgnoreCase(
                ((CaseInsensitiveString) o).s);
		
        if(o instanceof String)
            return s.equalsIgnoreCase((String) o);
        
        return false;
    }
    
    ... // 나머지 	
}

CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
```

* 대치성 위배의 잘못된 코드  
CaseInsensitiveString과 일반 String 객체가 하나씩 있다고 했을 때, cis.equals(s)는 true를 반환한다.

하지만 CaseInsensitiveString의 equals는 String을 알고 도록 개발자가 설계 했지만 String equals는 CaseInsensitiveString
의 존재를 모르는데 있다. 따라서 s.equals(cis)는 false 를 반환하여 대칭성을 위반한다는 것이다.

이를 해결하려면 어떻게 해야할까? 두 클래스의 equals 메서드를 연동하겠다는 생각을 버리면 된다.



```java
@Override public boolean equals(Object o) {
        return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);      
}
```

2. 추이성(transitivity)
null이 아닌 모든 참조값 x,y에 대해 x.equals(y)가 true이고, y.equals(z)도 true이면, x.equals(z)도 true이다.
  
첫번째 객체와 두번째 객체가 같고, 두번째 객체와 세번째 객체가 같으면, 첫번째 객체와 세번째 객체도 같아야 한다는 뜻이다. 이 규약은 상위클래스에 없는 새로운 필드를 하위 클래스에 추가하는 상황 같은 곳에서 어기기 쉽다.

* instanceof 연산자
자녀객체 instanceof 부모객체 == true  
부모객체 instanceof 자녀객체 == false  
객체 instanceof 객체타입 ==true  

```java
public class Point { 
    private int x; 
    private int y; 

    public Point(int x, int y) {
         this.x = x; 
        this.y = y; 
    }
     
    public boolean equals(Object o) {
        if (!(o instanceof Point)) {
            return false; 
           } 
        Point p = (Point) o; 
        return p.x == x && p.y == y; 
    } 
} 

public class ColorPoint extends Point { 
    private Color color;
    public ColorPoint(int x, int y, Color color) {
        super(x, y); 
        this.color = color; 
    }

// Point 구현이 상속되어 색상정보는 무시한채 비교를 수행.
public boolean equals(Object o) {
         if (!(o instanceof ColorPoint)) {
             return false; 
        } 
        return super.equals(o) && ((ColorPoint) o).color == color;    
} 	
```

* 위의 코드는 비교대상이 또다른 ColorPoint이고 위치와 색상이 같을때만 true를 반환.  

```java
ColorPoint cp = new ColorPoint(1, 2, Color.RED);
Point p = new Point(1, 2); 

p.equals(cp); //true
cp.equals(p); //false
```

* 그렇다면 ColorPoint.equals가 Point와 비교될때 색상을 무시하도록 하면 해결될까?
```java
public boolean equals(Object o) {
         if (!(o instanceof Point)) {
             return false; 
        } 
        else if (!(o instanceof ColorPoint)) {
        // o가 일반 Point면 색상을 무시하고 비교한다. 
            return o.equals(this); 
        } 
        // o가 ColorPoint면 색상까지 비교한다. 
        return super.equals(o) && ((ColorPoint) o).color == color; 
} 
```

* 이 방식을 대칭성을 지켜주지만 추이성을 깨버린다.

```java
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2); 
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE); 
p1.equals(p2); // true
p2.equals(p3); // true 
p1.equals(p3); // false
```


해결법은 무엇일까??
-----

### 상속대신 컴포지션을 사용해라!

* ColorPoint가 Point를 상속받지 않고, private Point point를 추가한다.

```java
public class ColorPoint{ 
    private final Color color;
    private final Point point;
    public ColorPoint(int x, int y, Color color) {
        point = new Point(x,y);
        this.color = Objects.requireNonNull(color);
    } 

    pulic Point asPoint(){
        return point;
    }
     
    public boolean equals(Object o) {
         if (!(o instanceof ColorPoint)) {
             return false; 
        } 
        ColorPoint cp = (ColorPoint ) o;
        return cp.point.equlas(point) && cp.color.equals(color);
}   

```

*일관성(consistency)

null이 아닌 모든 참조값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.


#### 강추 equals 메서드 구현 방법
1. == 연산자를 사용해 자기 자신의 참조인지 확인하라.
* 자기자신이면 true반환
2. instanceof 연산자로 입력이 올바른 타입인지 확인하라.
* null검사도 해줌
3. 입력을 올바른 타입으로 형변환하라.
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사하라.
5. 성능을 위해 다를 가능성이 더 크거나, 비용이 싼 필드를 먼저 비교해라.

* 위의 구현방법을 다 지켜 구현했다면 세가지만 자문해보자. 대칭적인가? 추이성이 있는가? 일관적인가? 자문에 끝내지 말고 단위테스트를 작성해 돌려보거나 AutoValue 애노테이션을 사용하자.




#### 결론은?
꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 많은 경우 Object의 equals가 여러분이 원하는 비교를 정확히 수행해준다. 재정의해야 할때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯가지의 규약을 확실히 지켜가며 비교해야 한다.


