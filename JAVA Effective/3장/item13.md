# 13. clone 재정의는 주의해서 진행하라

## Cloneable 인터페이스

* Cloneable 인터페이스에는 선언된 메소드가 없다.
- clone()은 최상위 클래스 Object에 protected 접근자로 선언되어 있다.
- 결론적으로 clone() 오버라이딩 = Object의 Clone()을 오버라이딩하는 일이 된다.
- 결국 상위클래스에서 정의된 protected 메소드(clone)의 동작 방식을 변경(override)하는 것이다.

* Clone 메소드의 일반 규약
clone()의 객체의 복사본을 생성해 반환한다.
'복제'의 일반적인 의도는 다음과 같다.
아래 식들은 일반적인 참이지만, 반드시 만족할 필요는 없다.

x.clone() != x       // 독립적인 객체이므로 주소 값은 다름  
x.clone().equals(x)  // 모든 필드값을 복사했으므로 equals는 true  
x.clone().getClass() == x.getClass() // 복제했으므로 클래스 타입은 동일  

* 관례상 clone 메소드가 반환하는 객체는 **super**.clone을 호출해 얻어야 한다.


## Clone 메소드 구현하기

1. Cloneable implements
2. @Override 해당클래스타입 public clone()에서 super.clone() 호출하여 상위 클래스 각체 얻기
3. 상위 클래스가 기본 타입의 필드만을 갖고 있거나, 불변 객체만을 참조한다면 신경쓸 것은 없음
4. 해당 클래스 타입으로 형변환하여 반화하기

```java
@Override
public CustomClass clone(){
	try{
		return (CustomClass) super.clone();
	} catch (CloneNotSupportedException e) {
		throw new AssertionError();
	}
}
```

* 이렇게 super.clone()을 호출하도록 clone()을 오버라이딩 하면, 알아서 필드 하나하나를 복사한 객체를 반환한다.

## Clone을 구현하는 클래스가 가변 객체를 참조하고 있을 경우

* 리스트나 사용자 정의 객체처럼 주소값만을 참조하고 있는 경우

```java
public class Stack implements Cloneable {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }
    
    @Override
    public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }

    // 원소를 위한 공간을 적어도 하나 이상 확보한다.
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

* int 타입의 size필드는 올바르게 복사된다.
* Object[] 타입의 elements 필드는 원본 Stack 인스턴스와 똑같은 배열을 참조한다.
- 즉 원본 인스턴스나 복제 인스턴스 한 쪽의 elements를 수정하면 두 인스턴스에 모두 영향을 미친다.
* clone()의 의도대로라면, 원본 객체에 영향을 미치지 않으면서 복제된 객체의 불변식을 보장해야 한다.

```java
/ 코드 13-2 가변 상태를 참조하는 클래스용 clone 메서드
@Override
public Stack clone() {
    try {
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```

1. super.clone()을 통해 상위클래스에거 객체를 가져와 Stack으로 형변환
2. elements를 한번 더 clone하여 result에 Setting

* 복제된 객체의 참조 필드를 재할당해야하므로, 가변 객체를 참조하는 필드는 final로 선언하라 라는 일반용법과 충돌한다.
* 복제 클래스를 만들기 위해 일부 필드에서 final을 제거할 수 있다.


```java
public class HashTable implements Cloneable {
    private Entry[] buckets = ...;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
        
        Entry deepCopy() {
            return new Entry(key, value, next == null ? null : next.deepCopy());
        }
    }

    @Override
    protected HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null)
                    result.buckets[i] = buckets[i].deepCopy();
            }
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

* 참조하는 객체가 내부적으로 또 다른 객체를 참조하고 있다면 어떻게 해야 할까
*deepCopy()를 재귀적으로 호출할 수 있으나 재귀 호출의 위험성 때문에 스택오버플로의 위험성이 있다.


```java
Entry deepCopy() {
        Entry result = new Entry(key, value, next);
        for (Entry p = result; p.next != null; p = p.next)
            p.next = new Entry(p.next.key, p.next.value, p.next.next);
        return result;
}
```

* 두번째 방법으로 반복자(Iterator)를 이용하여 복사할 수 있다.

### 요약

1. Cloneable을 구현하는 모든 클래스는 clone을 재정의해야 한다.
2. 접근제한자는 public 반환타입은 클래스 자신으로 선언되어
3. 가장 먼저 super.clone()을 호출한 후 필요한 필드로 형변환하여
 - 참조 객체라면 완벽히 deep copy 되어도 된다.
