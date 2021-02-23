# 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 이용하라.

```java
class Point { 
	public double x; 
	public double y; 
}
```

* 위 코드는 캡슐화의 이점을 제공하지 못한다.

* 모두 private로 바꾸고, public 접근자(getter)를 추가한다.
```java
class Point { 
	private double x; 
	private double y; 
	
	public Point(double x, double y) { 
		this.x = x; 
		this.y = y; 
	} 
	
	public double getX() { return x; } 
	public double getY() { return y; } 
	
	public void setX(double x) { this.x = x; } 
	public void setY(double y) { this.y = y; }
	
	}
```

위와 같은 방식이라면 패키지 바깥에서도 접근할 수 있는 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있다.


* 하지만 package-private(protected) class 또는 private 중첩 class 라면 public을 통해 필드를 노출한다 해여도 문제는 없다. 







