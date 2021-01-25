# 9. try-finally보다는 try-with-resources를 사용하라

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다.   
대표적으로 **InputStream** , **OutputStream** , **java.sql.Connection**    

상당수가 안전망으로 finalizer를 사용하지만 그리 믿을만하지 못하다 **(item8 참조)**  


try-finally
-----
close()를 할 수 있는 전통적인 방법
단일 자원이면 상관 없지만, close()를 해야하는 자원이 여러 개면 코드가 더러워진다.

```java
try {
	InputStream inputStream = new FileInputStream("src1"); 
} finally {
  inputStream.close();
}
```

* 자원이 2개인 경우

```java
static void copy(String src, String dst) throws IOException { 
	InputStream in = new FileInputStream(src); 
	try { 
		OutputStream out = new FileOutputStream(dst); 
		try { 
			byte[] buf = new byte[BUFFER_SIZE]; 
			int n; 
			while ((n = in.read(buf)) >= 0) { 
				out.write(buf, 0, n); 
				} 
			} finally {
			out.close(); 
			} 
		} finally { 
		in.close(); 
		} 
	}
```

*  가독성이 안좋다
*  예외는 try 블록과 finally 블록 모두에서 발생할 수 있다. try문과 finally문 모두에서 Exception 발생 시,  
finally에서 발생한 Exception만 보이기 때문에 디버깅이 어렵다.


java 7의 try-with-resources
-----
 **try-with-resources를 사용하려면 해당 자원이 AutoCloaseable 인터페이스를 구현해야한다.**  
 
 ```java
 static String firstLineOfFile(String path, String defaultVal) { 
	try (BufferedReader br = new BufferedReader( new FileReader(path))) {
		return br.readLine(); 
	} catch (IOException e) { 
		return defaultVal; 
	} 
}
 ```


  
대안 : AutoCloseable  
-----  
파일이나 스레드 등 종료해야 할 자원에 AutoCloaseable을 구현하나,  
이 때 예외가 발생하면 제대로 종료되도록 try-with-resources를 사용한다.    

 
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

* try-with-resources 버전이 짧고 읽기 수월할 뿐 아니라 문제를 진단하기도 훨씬 좋다. firstLineOfFile 메서드를 생각해보자. readLine 과 close 호출 양쪽에서 예외가 발생하면, close에서 발생한 예외는 숨겨지고 readLine에서 발생한 예외가 기록된다.




