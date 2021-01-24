8. finalizer와 cleaner 사용을 피하라.  
=====

* 자바에서는 finalizer와 cleaner라는 두 가지의 객체 소멸자를 제공한다.  

사용을 피해야하는 이유
-----
* 실행되기까지 얼마나 걸릴지 알 수 없다.
 -> 제때 실행해야하는 작업에서 사용 불가
* 수행여부를 보장하지 않는다.
* finalizer 동작 중 발생하는 예외는 무시된다. 
  
```java
public class Test {
    public static void main(String[] args) {
        ObjB obj = new ObjB(3, "madplay");
        obj.finalize();
    }


    public static class ObjA {
        private int id;
    
        public ObjA(int id) {
            this.id = id;
            System.out.println("Call ObjA Constructor");
        }
    
        public void finalize() {
            System.out.println("Call ObjA Destructor");
        }
    }
    
    public static class ObjB extends ObjA {
        private String name;
    
        public ObjB(int id, String name) {
            super(id);
            this.name = name;
            System.out.println("Call ObjB Constructor");
        }
    
        public void finalize() {
            System.out.println("Call ObjB Destructor");

            // ★ 자바는 상위 객체의 finalizse를 호출하지 않기 때문에 꼭 명시가 필요하다.
            super.finalize();
        }
    }
} 
```  


  
  
대안 : AutoCloseable  
-----  
파일이나 스레드 등 종료해야 할 자원에 AutoCloaseable을 구현하나,  
이 때 예외가 발생하면 제대로 종료되도록 try-with-resources를 사용한다.    



* 자바를 이용하여 외부 자원에 접근하는 경우 코드이다.
* 자원을 제대로 닫아주기 위해선 아래와 같이 보기 힘들고 장황하게 되어 있다.
 
```java
public static void main(String[] args) {
    FileInputStream fis = null;
	
	try{
		 fis = new FileInputStream("");
	}catch(IOException e){

	}finally {
		try {
			// 객체 존재가 존재하는지 체크 후에 close()
			if(fis != null) {
				fis.close();
			}
		}catch (IOException ie){

		}
	}
}
```


#### 그래서 JDK1.7에는 AutoCloseable 인터페이스가 추가됐다.  

```java
/**
 * @author Josh Bloch
 * @since 1.7
 */
public interface AutoCloseable {
    void close() throws Exception;
}
```

* 인터페이스 추가와 함께 try문의 소소한 문법도 개선되었다.  

```java
public static void main(String[] args) {
    try(FileInputStream fis = new FileInputStream("")){
         
    }catch(IOException e){

    }
}
```

* 위와 같이 try(){} 형태로 사용이 가능하며 () 안에 들어올 수 있는건 AutoCloseable 구현체뿐이다.     
 -> 이런 문법을 **try with resources** 라고 부른다. 
 
#### 저렇게하면 무슨 이점이 있을까? 
* 매우 직관적인 인터페이스 명칭이나 위 예제를 보고 눈치챌수도있겠지만  try catch 절이 종료되면서 자동으로 close() 메서드를 호출해준다.


##### 예제 1
```
class A implements AutoCloseable{
    @Override
    public void close() throws Exception {
        System.out.println("a call");
    }
}
```
* Autocloaseable 인터페이스를 상속받아 close() 메서드를 구현하였다.  

```java
public static void main(String[] args) {
    try(A a = new A()){

    }catch(Exception e){

    }
}
```




##### 예제 2

* A,B 클래스에 AutoCloseable 인터페이스를 상속하였다.

```java
class A implements AutoCloseable{
    @Override
    public void close() throws Exception {
        System.out.println("a call");
    }
}

class B implements AutoCloseable{
	private A a;
	
    public B(A a){
		this.a = a;
    }

    @Override
    public void close() throws Exception {
        System.out.println("b call");
    }
}
```
* 아래와 같이 try-with-resources를 구현하면 B에 대한 객체만 close 된다.
  
```java
public static void main(String[] args) {
    try(B b = new B(new A())){

    }catch(Exception e){

    }
}
```


* try() 문은 자신의 변수가 아닌 객체의 close()를 호출해주진 않는다. 
* 이것은 위 예제에서 증명할 수 있다. 하지만 io 패키지에서 제공하는 Stream 객체들은 내부 필드에 대한 close()도 호출이 된다. 
* 이는 try() 문이 해주는것은 아니며, 데코레이터 패턴을 활용한 객체 내에서 자신의 필드에 대한 close()도 호출해주기 때문이다. AutoCloseable 의 구현체를 작성할때는 해당부분도 유의해서 작성하자.  

```java
class B implements AutoCloseable {
   private A a;

   public B(A a) {
      this.a = a;
   }

   @Override
   public void close() throws Exception {
      this.a.close();
      System.out.println("b call");
   }
}
```




