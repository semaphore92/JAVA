# 15. 클래스와 멤버의 접근 권한은 최소화하라

* 컴포넌트 설계 시 중요한 점은 클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 잘 숨겼는지(은닉화)가 중요하다.

### 정보 은닉 장점

1. 시스템 개발 속도를 높인다.
2. 시스템 관리 비용을 낮춘다.
3. 성능 최적화에 도움을 준다.
4. 소프트웨어 재사용을 높인다.
5. 큰 시스템을 제작하는 난이도를 낮춘다.

* 내용의 핵심은 클래스와 멤버의 접근제한자를 private등으로 낮은 접근 수준을 부여하는 것

### 자바 접근 제한자

* public (어떤 클래스라도 접근 가능)
* protected (동일 패키지 내에 있는 클래스와 하위 클래스에서만 가능)
* default (동일 패키지에서만 접근 가능)
* private (클래스 내에서만 접근 가능)


### 자바 배열은 변경 가능하니 접근자에 주의한다.

클래스에 public static final 배열 필드를 두거나 해당 배열을 반환하는 접근자 메서드를 제공하면 안된다.
```java
public static final Thing[] VALUES = {...};
```
해결하는 방법은 두 가지다.

* public을 private로 변경하고 public 불변 리스트를 추가한다.

```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final LIST<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

* 두 번째 방법은 배열을 private으로 만들고, 원본이 아닌 복사본을 반환하는 public 메서드를 추가하는 것이다.
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] values() {
	return PRIVATE_VALUES.clone();
}
```






