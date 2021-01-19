# 6. 불필요한 객체 생성을 피하라

* 자주 사용되는 객체가 있다면 이는 매번 생성하기보다는 객체 하나를 재사용하는 것이 훨씬 빠르고 효율적이다.

## 객체의 재사용

* 아래와 같이 Boolean객체를 사용할 때 아래와 같이 작성하면 매번 새로운 객체가 생성된다.
```java
Boolean trueObject = new Boolean(true);
Boolean falseObject = new Boolean(false);
```


* 별도의 객체를 생성하지 않고, 기존에 만들어진 객체를 그대로 재활용
```java
Boolean trueObject = Boolean.TRUE;
Boolean falseObject = Boolean.FALSE;
```


* String 객체를 사용할 때 new를 통해 객체를 생성하게 되면, 계속 새로운 객체를 생성하게 된다.
* 아래 코드에서 text1을 선언하면 힙메모리에 "text"가 저장되고, 이후에 "text"를 다시 선언하면 힙 메모리에 있는 "text"를 참조한다.
```java
String newStringText1 = new String("text"); // 주소 : 0x0001
String newStringText2 = new String("text"); // 주소 : 0x0002
String newStringText3 = new String("text"); // 주소 : 0x0003

String text1 = "text"; // 주소 : 0x0004
String text2 = "text"; // 주소 : 0x0004
String text2 = "text"; // 주소 : 0x0004
```



## 생성 비용이 큰 객체 재사용
* 생성 비용이 큰 객체가 있다면 이는 매번 생성하기보다는 객체 하나를 재사용하는것이 훨씬 빠르고 효율적이다.
* 다음은 문자열이 유효한 로마 숫자인지를 확인하는 메서드를 작성한다고 해보자.
```java
public static boolean isRomanNumeral(String s){
	return s.matches("^(?=[MDCLXVI])M*D?C{0,4}L?X{0,4}V?I{0,4}$"); 
}
```


* matches의 내부를 보면 아래와 같이 Pattern객체를 통해 regex문자열을 compile하는 것을 볼 수 있는다. 또한 compile함수가 객체를 내부적으로 생성하는 것 또한 볼 수 있다.
```java
public static boolean matches(String regex, CharSequence input) {
  Pattern p = Pattern.compile(regex);
  Matcher m = p.matcher(input);
  return m.matches();
}

public static Pattern compile(String regex) {
	return new Pattern(regex, 0);
}
```

* matches가 계속 호출되면 Pattern 객체를 지속적으로 생성한다.
* 아래와 같이 변경할 수 있다.
```java
public class RomanNumerals{
	private static final Pattern ROMAN = 
		Pattern.compile("^(?=[MDCLXVI])M*D?C{0,4}L?X{0,4}V?I{0,4}$");
        
	public static boolean isRomanNumeral(String s){
		return ROMAN.matcher(s).matches();
	}
}
```

### 특히 요즘 JVM에서는 별다른 일을 하지 않는 작은 객체를 생성하고 회수히는 일이 크게 부담되지 않는다. 고로 프로그램의 명확성, 간결성, 기능을 위해서 객체를 추가로 생성하는 것이라면 일반적으로 좋은 일이다.