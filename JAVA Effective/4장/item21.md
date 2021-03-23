# 21. 인터페이스는 구현하는 쪽을 생각해 설계하라

* 모든 상황에서 불변식을 해치지 않는 디폴트 메서드를 작성하기란 어렵다.
* 디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.
* 인터페이스를 설계 할 때는 세심한 주의를 기울여야 한다.


자바 8의 Collection 인터페이스에 추가된 디폴트 메서드
```java
	default boolean removeIf(Predicate<? super E> filter){
		Objects.requireNonNull(filter);
		boolean result = false;
		
		for(Iterator<E> it = iterator(); it.hasNext()){
			if(filter.test(it.next())){
				it.remove();
				result = true;
			}
		}
		return result;
	}
```

### 디폴트 메서드가 생겼더라도 인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야한다.
        
		
		
		
### 디폴트 메서드란?
* Java 8에서는 디폴트 메서드라는 것을 사용하여 메서드 구현을 포함하는 인터페이스를 정의할 수 있다.  인터페이스에서 이미 구현을 했으니 해당 인터페이스를 구현하는 클래스에서는 추가된 메서드의 구현을 할 필요가 없다.
  
* 결과적으로 기존 인터페이스를 구현하는 클래스는 자동으로 인터페이스에 추가된 새로운 메서드의 디폴트 메서드를 상속 받게 된다.

  
















