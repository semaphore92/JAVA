# 23. 태그 달린 클래스보다는 클래스 계층 구조를 활용화하라

## 태그 달린 클래스
* 쓸데 없는 코드가 너무 많다 : 열거 타입선언, 태그 필드, switch문
* 장황하고 오류를 내기 쉽고 비효율 적이다.
* 클래스 계층 구조를 어설프게 흉내낸 것이다.

```java
	class Figure {
		enum Shape { RECTANGLE, CIRCLE };
		
		// 태그 필드 - 현재 모양을 나타낸다.
		final Shape shape;
		
		// 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
		double width;
		double height;
		
		// 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
		double radius;'
		
		// 이하 코드 생략
	}
```
이러한 클래스는 단점이 가득하다.  
우선 열거 타입 선언, 태그 필드, switch문 등 쓸데 없는 코드가 많다.  
여러 구현이 한 클래스에 혼합돼 있어서 가독성도 나쁘다. 
** 태그 달린 클래스는 장황하고 오류를 내기 쉽고, 비효율적이다 **

## 태그 달린 클래스를 계층 구조로 바꾸는 법

```java
	abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;
    
    Circle(double radius) { this.radius = radius; }
    
    @Override
    double area() {
    	return Math.PI * (radius * radius);
    }
}

class Reactangle extends Figure {
    final double length;
    final double width;
    
    Rectangle(double length, double width) {
    	this.length = length;
        this.width = width;
    }
    
    @Override
    double area() {
    	return length * width;
    }
}
````