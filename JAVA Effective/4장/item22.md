# 22. 인터페이스는 타입을 정의하는 용도로만 사용하라.

* 인터페이스의 용도
인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 **타입 역할**을한다.  
즉, 클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 이야기 해주는 것이다.

### 안티패턴

```java
	public interface AntiPhsicalConstans {
		
		static final double AVOGADROS_NUMBER = 6.022_140_857e23;
		
		static final double boltzmann_constant = 1.380_648_52e-23;
		
		static final double ELECTRON_MASS = 9.109_383_56E-31;
	
	}
```

* 위와 같이 메서드 없이, 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스의 형태는 상수 인터페이스로 불리며 인터페이스의 안티 패턴이다.
  
  

#### 상수 인터페이스의 문제점
클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당한다.  
따라서 상수 인터페이스를 구현하는 것은 이 내부 구현을 클래스의 API로 노출하는 행위이다.
만약 클래스가 상수 인터페이스를 상속받아서 구현한다면 모든 하위 클래스의 공간에 인터페이스 상수들로 오염되어 버린다.
  
  
#### 해결 방법
특정 클래스나 인터페이스와 강하게 연관된 상수라면 그 클래스나 인터페이스 자체에 추가해야한다.

``` java		
	public class PhysicalConstants {
		private PhysicalConstants(){}
		
		static final double AVOGADROS_NUMBER = 6.022_140_857e23;
		
		static final double boltzmann_constant = 1.380_648_52e-23;
		
		static final double ELECTRON_MASS = 9.109_383_56E-31
	}
```
