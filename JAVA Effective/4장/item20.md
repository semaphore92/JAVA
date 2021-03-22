# 20. 추상클래스보다는 인터페이스를 우선하라.

* 인터페이스와 추상클래스는 자바 8부터 default 키워드를 통해 default 메서드를 제공할 수 있게 되었다.

```java
	public interface Hello {
		default void say(){
			System.out.println("Hello World");
		}
	}
```
### 추상클래스(Abstract)
추상클래스가 정의한 타입을 구현하는 클래스는 추상클래스의 하위 타입이 되어야한다.

### 인터페이스 (implements)
인터페이스가 선언한 메서드를 모두 정의하고 일반적 규약을 잘 지킨 클래스면 어떤 클래스를 상속해도 같은 타입으로 이용된다.

1. 인터페이스는 믹스인 정의에 안성맞춤
	**믹스인이란?**  
	기존에 클래스의 주된 타입 외에 선택적 행위를 제공하는 것  
	예를들어 Compareable은 자신을 구현한 클래스들의 순서를 정할 수 있다고 선언하는 인터페이스이다.
	
2. 인터페이스의 유연성
	인터페이스는 계층구조가 없는 타입 프레임워크를 만들 수 있다.  
```java
	public Interface Singer {
		AudioClip sing(Song s);
	}
```	

```java
	public Interface Songwriter {
		Song compose(int chartPosition);
	}
```	
* 위와 같이 가수와 작곡가 역할을 하는 인터페이스가 있다. 하지만 현실에서는 작곡을 함께하는 가수 존재한다.

```java 
	public interface SingerSongwriter extends Singer, Songwriter{
		AudioClip strum();
		void actSensitive();
	}
```

### 인터페이스와 추상 클래스의 차이
두 메커니즘의 차이는 추상클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야한다는 점이다.
자바는 단일 상속만 지원하니, 추상 클래스 방식은 새로운 타입을 정의하는데 커다란 제약이 생긴다. 반면 인터페이스가 선언한 메서드를 모두 
정의하고 그 일반 규약을 잘 지킨 클래스라면 다른 어떤 클래스를 상속했든 같은 타입으로 취합한다.

### 추상 골격 구현(skeletal implementation) 클래스
인터페이스로는 타입을 정의하고, 필요하다면 디폴트 메서드 몇 개도 함께 제공한다.  
골격 구현 클래스는 나머지 메서드들까지 구현하는 방법을 사용하면 단순히 골격 구현을 확장하는 것만으로 필요한 일이 완료된다.














