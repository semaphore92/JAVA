# 2. 생성자에 매개변수가 많다면 빌더를 고려하라.




선택적 매개변수가 많다면 사용할 수 있는 방식
-------------------------------

### 점층적 생성자 패턴
```java
public class NutritionFacts {
	private final int servingSize;
	private final int servings;
	private final int calories;
	private final int fat;
	private final int sodium;
	private final int carbohydrate;
	
	public NutritionFacts(int servingSize, int servings){
		this(servingSize, servingSize, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories ,int fat){
		this(servingSize, servingSize, calories, fat, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories ,int fat, int sodium){
		this(servingSize, servingSize, calories, fat, sodium, 0);
	}
	
	public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate {
		this.servingSize = servingSize;
		this.servings = servings
		this.calories = calories
		this.fat = fat;
		this.sodium = sodium;
		this.carbohydrate = carbohydrate;
	}
}
```
위오 같은 점층적 생성자 패턴으로 인스턴스 생성 시, 원하는 생성자를 골라 호출하면 된다.

```java
	NutritionFacts cocaCola = new NutritionFacts(240, 8 , 100, 0, 35, 27);
```

* 매개변수의 개수가 많아지면 코드의 확장이 어렵다.



### 자바빈즈 패턴
```java
public class NutritionFacts {
	private int servingSize = -1;
	private int servings = -1;
	private int calories = 0;
	private int fat = 0;
	private int sodium = 0;
	private int carbohydrate = 0;
	
	public NutritionFacts(){ }
	public void setServingSize(int val) { servingSize = val; }
	public void setServings(int val) { servings = val;}
	public void setCalories(int val) { calories = val;}
	public void setFat(int val) { fat = val;}
	public void setSodium(int val) { sodium = val;}
	public void setCarbohydrate(int val) { carbohydrate = val;}
	
}
```

* 객체를 하나 생성하려면 메서드를 여러 개 호출해야한다.
* 객체가 생성되기 전까지는 일관성이 무너진 상태에 놓인다.



### 빌드 패턴

점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 합친 빌드패턴은,
필수 매개변수로만 생성자를 호출하여 빌더 객체를 얻는다.


```java
public class NutritionFacts {
	private final int servingSize;
	private final int servings;
	private final int calories;
	private final int fat;
	private final int sodium;
	private final int carbohydrate;
	
	public static class Builder {
		// 필수 매개변수
		private final int servingSize;
		private final int servings;
		
		// 선택 매개변수
		private int calories     = 0;
		private int fat          = 0;
		private int sodium       = 0;
		private int carbohydrate = 0;
		
		// 필수 매개변수는 생성자
		public Builder(int servingSize, int servings){
			this.servingSize = servingSize;
			this.servings    = servings;
		}
		
		public Builder calories(int val){
			calories = val;
			return this;
		}
		
		public Builder fat(int val){
			fat = val;
			return this;
		}
		
		public Builder sodium(int val){
			sodium = val;
			return this;
		}
		
		public Builder carbohydrate(int val){
			carbohydrate = val;
			return this;
		}
		
		public NutritionFacts build(){
			return new NutritionFacts(this);
		}
	
	}
	
	private NutritionFacts (Builder builder){
		servingSize 	= builder.servingSize;
		servings 		= builder.servings;
		calories 		= builder.calories;
		fat 			= builder.fat;
		sodium			= builder.sodium;
		carbohydrate	= build.carbohydrate;
	}

}
```
NutritionFacts 클래스는 불변이며, 모든 매개변수의 기본 값들을 한곳에 모아뒀다.

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                                            .calories(100)
                                            .sodium(35)
                                            .carbohydrate(27).build();
```

위와 같이 인스턴스를 생성할 수 있다.											



#### 빌더 패턴 예제

```java
public abstract class Pizza {
	public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
	fianl Set<Topping> toppings;
	
	abstract static class Builder<T extends Builder<T>> {
		EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
		public T addTopping(Topping topping){
			toppings.add(Objects.requireNonNull(topping));
			return self();
		}
		
		abstract Pizza build();
		
		// 하위 클래스는 이 메서드를 재정의하여 "this"를 반환하도록 한다.
		// 자식 클래스에서 형변환 없이 메서드 연쇄를 지원한다.
		protected abstract T self();
	}
	
	Pizza(Builder<?> builder){
		toppings = builder.toppings.clone();
	}
}
```
* Pizza.Builder 추상 클래스를 이용하여 하위클래스를 생성해보겠다.

##### 뉴욕 피자
```java
public class NyPizza extends Pizza {
	public enum Size { SMALL, MEDIUM, LARGE }
	private final Size size;
	
	public static class Builder extends Pizza.Builder<Builder> {
		private final Size size;
		
		public Builder(Size size){
			this.size = Objects.requireNonNull(size);
		}
		
		// 구체 클래스를 반환하지 않고 구체의 하위 클래스를 반환함으로써 형변환에 신경쓰지 않아도 된다. 이를 공변 변환 타이핑이라 한다.
		@Override 
		public NyPizza build(){
			return new NyPizza(this);
		}
		
		@Override
		protected Builder self(){
			return this;
		}
	}
	
	private NyPizza(Builder builder) {
		super(builder);
		size = builder.size;
	}
}
```

```java
	NyPizza pizza = new NyPizza.Builder(SMALL)
					.addTopping(SAUSAGE).addTopping(ONION).build();
```
* 각 하위 클래스의 빌더가 정의한 build 메서드는 해당하는 구체 하위 클래스를 반환하도록 선언한다. 실제 Pizza의 build는 Pizza를 반환하지만, 이를 Overriding한 NyPizza의 build는 NyPizza를 리턴한다.
* 이는 메서드를 Overriding할 때, 상위 클래스의 메서드가 반환하는 클래스 타입을 그대로 반환하는 것이 아니라, 하위 클래스에서 해당 메서드의 반환 되는 타입을 하위 타입으로 반환이 가능하도록 해주는데 이를 공변 반환 타이핑(covariant return typing)이라 한다.




