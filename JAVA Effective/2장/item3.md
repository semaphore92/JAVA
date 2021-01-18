# 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라




싱글턴 (singleton)
-------------------------------
인스턴스를 오직 하나만 생성할 수 있는 클래스

### 싱글턴의 한계
1. private 생성자를 가지고 있기 때문에 상속할 수 없다.
2. 싱글턴은 테스트하기가 어렵다.
3. 서버환경에서는 싱글턴이 하나만 만들어지는 것을 보장하지 못한다.
4. 싱글톤의 사용은 전역 상태를 만들 수 있기 때문에 바람직하지 못하다.



### 싱글턴 사용 이유
한번의 객체 생성으로 재 사용이 가능하기 때문에 메모리 낭비를 방지할 수 있다.



## 싱글턴을 만드는 방식



### 1. public static 멤버가 final 필드인 방식
```java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
}
```
#### 장점
* 해당 클래스가 싱글턴임이 API에 명백히 드러난다.


#### 예외 
* 리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수 있다.
* 리플렉션 API: java.lang.reflect, class 객체가 주어지면, 해당 클래스의 인스턴스를 생성하거나 메소드를 호출하거나, 필드에 접근할 수 있다.
```java
Constructor<Elvis> constructor = (Constructor<Elvis>) elvis2.getClass().getDeclaredConstructor();
constructor.setAccessible(true);

Elvis elvis3 = constructor.newInstance();
assertNotSame(elvis2, elvis3); // FAIL
```


#### 해결 방법
* 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던진다.
```java
private Elvis() {
	if(INSTANCE != null){
		throw new RuntimeException("생성자를 호출할 수 없습니다!");
	}
}
```



### 2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
```java
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
	public static Elvis getInstance() { return INSTANCE; }
}
```


#### 장점
* API를 바꾸지 않고 싱글턴을 해제할 수 있다. (getInstance() 호출 없이 내부에서 새 인스턴스를 생성)




### 두 방식의 문제점
* 각 클래스를 직렬화한 후 역질렬화할 때 새로운 인스턴스를 만들어서 반환.

이를 방지하기 위해 readResolve 에서 싱글턴 인스턴스를 반환하고, 모든 필드에 transient(직렬화 제외) 키워드를 넣는다.

```java
private Obejct readResolve() {
	return INSTANCE;
}
```

### 참고 : https://velog.io/@lychee/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-3.-private-%EC%83%9D%EC%84%B1%EC%9E%90%EB%82%98-%EC%97%B4%EA%B1%B0-%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C-%EC%8B%B1%EA%B8%80%ED%84%B4%EC%9E%84%EC%9D%84-%EB%B3%B4%EC%A6%9D%ED%95%98%EB%9D%BC

