# 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.




## 정적 유틸리티 클래스
```java
public class SpellChecker {
	private static final Lexicon dictionary = ...;
    
    private SpellChecker() {} // 인스턴스화 방지 (아이템 4 참고)
    
    public static boolean isVaild(String word) {...}
    public static List<String> suggestions(String typo) {...}
}
```


## 싱글턴
```java
public class SpellChecker {
	private final Lexicon dictionary = ...;
    
    private SpellChecker() {} // 인스턴스화 방지 (아이템 4 참고)
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public static boolean isVaild(String word) {...}
    public static List<String> suggestions(String typo) {...}
}
```


* 위와 같은 두 방법은 확장에 유연하지 않고 테스트가 어렵다.
* 가령 사전은 굉장히 여러 종류가 있는데 사전이 바뀔때마다 모든 역할을 수행하기가 어렵다.

### 사용 자원에 따라 동작이 달라지는 클래스는 어떻게 작성할까??

## (팩토리 메서드 패턴)

```java
public class SpellChecker {
    private final Lexicon dictionary;
    
    // 여기서 의존성 주입을!
	// 인터페이스의 다형성을 이용하여 의존 주입
    public SpellChecker(Lexicon dictionary){
    	this.dictionary = Objects.requireNotNull(dictionary);
    }
    
    public static boolean isVaild(String word) {...}
    public static List<String> suggestions(String typo) {...}
}

// 인터페이스
interface Lexicon {}

// Lexicon을 상속 받아서 구현
public class myDictionary implements Lexicon {
	...
}

// 사용은 이렇게!
Lexicon dic = new myDictionary();
SpellChecker chk = new SpellChecker(dic);

chk.isVaild(word);
```