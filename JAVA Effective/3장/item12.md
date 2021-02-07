# 12. toString을 재정의 해야하는 이유

* Object의 기본 toString 적합한 문자열을 반환하는 경우는 거의 없다.  이 메서드는 **16진수로 표현한 해시코드** 를 반환할 뿐이다.
* toString의 일반 규약에 따르면 '간결하면서 사람이 읽기 쉬운 형태의 유익한 정보'를 반환해야 한다.   
 이러한 이유 때문에 toString의 규약은 모든 '하위클래스에서 이 메서드를 재정의하라' 라고 하고 있다.  
 

toString() 재정의 예제
 -----
 ```java
 public final class PhoneNumber {
   private final short areaCode, prefix, lineNum;
   
   public PhoneNumver(int areaCode, int prefix, int lineNum){
      // 생성자
   }
   
   @Override public String toString() {
      return String.format("%03d-%03d-%04d", areaCode,prefix,lineNum);
   }
 }
 
 ```
   
 ```java
 PhoneNumber jenny = new PhoneNumber(707, 867, 5309);
 System.out.println("제니의 번호: " + jenny);
 ```
 
 * println을 했을 때 클래스@해시코드보다 실제 유용한 메시지가 나오는게 훨씬 보기 편할 것이다.
 * 실전에서 toString은 객체가 가진 주요 정보 모두를 반환하는 것이 좋다.
 
 
 ### toString() 재정의 시, 주의할 점
 
 1. toString을 구현할 때면 반한 값의 포맷을 문서화 할지 정해야 한다.
 2. 포맷을 명시하기로 했다면 명시한 포맷에 맞는 문자열과 객체를 상호 전환할 수 있는 정적 팩터리나 생성자를 함께 제공해주면 좋다.
 3. 포맷을 한번 명시하면 평생 포맷에 얽매이게 되는 단점이 있다.
 4. 의도를 명확히 밝혀야 한다.
 
 
 
 ### 정리
 * 모든 구체 클래스에서는 Object의 toString을 재정희하자.
 * toString을 재정의한 클래스는 디버깅을 쉽게 해준다.
 * toString은 객체에 대한 명확하고 유용한 정보를 읽기 좋은 형태로 반환해야 한다.