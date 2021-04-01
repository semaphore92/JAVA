# 24. 멤버 클래스는 되도록 static으로 만들어라

* 중첩 클래스란 다른 클래스 안에 정의된 클래스를 말한다.
* 중첩 클래스의 종류로 정적 멤버 클래스, 멤버 클래스, 익명 클래스, 지역 클래스가 있다.

### 중첩클래스는 왜 사용하는가?
1. 내부 클래스에서 외부 클래스의 멤버에 손쉽게 접근할 수 있다.
2. 서로 관련 있는 클래스들을 논리적으로 묶어, 코드의 캡슐화를 증가시킬 수 있다.
3. 외부에서 내부 클래스에 접근할 수 없으므로 코드의 복잡성을 줄일 수 있다.  

## 정적 멤버 클래스
* 클래스는 다른 클래스 안에 선언되고, 바깥 클래스의 private 멤버에도 접근할 수 있다.  
* 정적  멤버 클래스는 외부 클래스를 사용할때 자연스럽게 내부 클래스를 호출하여 외브 클래스의 생성을 도와주는 헬퍼 클래스이다.
```java
	public class NutritionFacts {
	  private final int servingSize;
	  private final int servings;
	  private final int calories;
	  private final int fat;

	  public static class Builder {
		// 필수 매개변수
		private final int servingSize;
		private final int servings;

		// 선택 매개변수 - 기본값으로 초기화한다.
		private int calories      = 0;
		private int fat           = 0;

		public Builder(int servingSize, int servings) {
		  this.servingSize = servingSize;
		  this.servings    = servings;
		}

		public Builder calories(int val) {
		  calories = val;      
		  return this; 
		}
		public Builder fat(int val) {
		  fat = val;           
		  return this; 
		}

		public NutritionFacts build() {
		  return new NutritionFacts(this);
		}
	  }

	  public NutritionFacts(Builder builder) {
		servingSize  = builder.servingSize;
		servings     = builder.servings;
		calories     = builder.calories;
		fat          = builder.fat;
	  }

	  public static void main(String[] args) {
		NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
		  .calories(100).build();
	  }
	}
	
```


## 비정적 멤버 클래스  
* 비정적 멤버 클래스의 경우 바깥 클래스의 인스턴스와 암묵적으로 연결된다.

```java
	public class ClassA {
	  public int a = 0;

	  public void print() {
		System.out.println("pring ClassA");
		ClassB b = new ClassB();
		b.print();
	  }

	  public class ClassB {
		public void print() {
		  //ClassA.this를 통해 ClassA에 접근이 가능.
		  System.out.println("print ClassB : " + ClassA.this.a);
		}
	  }
	}


	public class ClassAMain {
		public static void main(String[] args) {
			ClassA a = new ClassA();
			a.print();
					System.out.println("================");
			ClassA.ClassB b = a.new ClassB();
			b.print();
		}
	}
```

  
## 익명 클래스
* 익명 클래스는 이름이 없는 클래스이고 외브 클래스의 멤버 클래스도 아니다. 또한 익명 클래스가 상위 타입에 상속한 멤버 외에는 호출할 수가 없다.

```java
	public class AnonymousClsParent {
	  public void print() {}
	}

	public class AnonymousCls extends AnonymousClsParent{
	  public void save() {
		System.out.println("save");
	  }
	}

	public static void main(String[] args) {
	  AnonymousCls a = new AnonymousCls() {
		public String cd = "copy";
		public void copy() {
		  System.out.println("copy");
		}

		@Override
		public void save() {
		  System.out.println("save from main");
		  super.save();
		}


		@Override
		public void print() {
		  System.out.println("print from main");
		  super.print();
		}
	  };

	  //a.copy() 호출 불가
	  a.save();
	}

	//결과
	//save from main
	//save
	//print from main	
```


  
### 결론  
멤버 클래스가 외부 클래스의 인스턴스를 참조하지 않는다면, 멤버 클래스는 static으로 만드는 것이 좋다.  
멤버 클래스를 static으로 만들게 되면 외부 클래스를 반복적으로 생성할 때, 내부 인스턴스를 반복적으로 재생성할 비용이 사라지고 heap 영역의 메모리 공간을 낭비하지 않게 된다.
