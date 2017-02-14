### 객체의 생성과 삭제

#### Rule 1. 생성자 대신 정적 팩토리 메서드를 사용할 수 없는지 생각해보라

**일반적인 생성자 함수 방식**
```java
public class Test{
public Test(){
...
}
```

**정적 팩토리 메서드 방식**
```java
public class Test{
	private static final Test INSTANCE = new Test();
	
	private test(){}
	
	public static Test getInstance(){
		return INSTANCE;
	}
```
장점 : 
 
* 일반 생성자처럼 클래스 이름을 부여하는게 아니라 함수 기능에 맞는 이름을 부여할 수 있다. (new Test vs Test.getInstance)
* 생성할 때마다 객체를 새로 만들지 않기 때문에, 싱글톤 디자인에 적합
// 싱글톤은 객체를 하나만 만들 수 있는 클래스다
* 코드가 깔끔해질 수 있음


#### Rule 2. 생성자 인자가 많을 때는 Builder pattern 적용을 고려하라

#### Rule 3. private 생성자나 enum 타입을 사용해서 싱글톤의 특성을 유지하자

#### Rule 4. private 생성자를 사용해서 인스턴스 생성을 못하게 하자

#### Rule 5. 불필요한 객체의 생성을 피하자

#### Rule 6. 쓸모 없는 객체 참조를 제거하자

- 만기 참조를 제거하는 가장 좋은 방법은 해당 참조가 보관된 변수가 scope를 벗어나게 두는 것이다. 변수를 정의할 때 그 유효범위를 최대한 좁게 만들면 자연스럽게 해결된다.
- 객체 참조를 null 처리하는 것은 규범이라기 보단 예외적인 조치가 되어야 한다.
- 메모리 누수가 흔히 발견되는 또 한 곳은 리스너(listener)등의 callback이다. callback 등록 기능을 제공하는 api를 사용하는 클라이언트가 콜백을 명시적으로 제거하지 않을 경우, 적절한 조치를 취하기 전까지 메모리는 점유된 상태로 남아있게 된다.
- ** garbage collector가 콜백을 즉시 처리하도록 할 가장 좋은 방법은, 콜백에 대한 약한 참조(weak reference)만 저장하는 것이다. WeakHashMap의 키로 저장하는 것이 그 예다 // TODO : Detail

#### Rule 7. 종료자 사용을 피하라.

<br/>


### 모든 객체의 공통 메서드

#### Rule 8. equals를 재정의할 때는 일반 규약을 따르라

**일반 규약 (Java SE6)**

- 반사성 (reflexive) : null 이 아닌 참조 x가 있을 때, x.equals(x)는 true를 반환한다. 다시 말해, 모든 객체는 자기 자신과 같아야 한다.
- 대칭성 (symmetric) : null 이 아닌 참조 x와 y가 있을 때, x.equals(y)는 y.equals(x)가 true일 때만 true를 반환한다. 다시 말해, 두 객체에게 서로 같은지 물으면 같은 답이 나와야 한다.
- 추이성 (transitive) : null 이 아닌 참조 x, y, z가 있을 때, x.equals(y)가 true이고 y.equals(z)가 true이면 x.equals(z)도 true이다. 
- 일관성 (consistent) : null이 아닌 참조 x와 y가 있을 때, equals를 통해 비교되는 정보에 아무 변화가 없다면, x.equals(y) 호출 결과는 호출 횟수에 상관없이 항상 같아야 한다. 다시 말해, 일단 같다고 판정된 객체들은 추후 변경되지 않는 한 계속 같아야 한다.
- null이 아닌 참조 x에 대해서, x.equals(null)은 항상 false이다.

#### Rule 9. equals를 재정의할 때는 반드시 haseCode도 재정의하라

#### Rule 10. toString은 항상 재정의하라

#### Rule 11. clone 메소드는 신중하게 오버라이드 하자

#### Rule 12. Comparable 인터페이스의 구현을 고려하자
만일 comparable을 구현해 놓으면, 그 객체들의 배열을 정리하는 것은 Arrays.sort(a); 와 같이 쉽게 수행될 수 있다.

**Comparable Interface**
```java
public interface Comparable<T> {
  int compareTo(T t);
}
...
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString> { ... }
```

<br/>

### 클래스와 인터페이스

#### Rule 13. 클래스와 멤버의 접근 권한은 최소화하라

#### Rule 14. public class 안에는 public field를 두지 말고 접근자 메서드를 사용하라

#### Rule 15. 변경 가능성을 최소화하라

#### Rule 16. 계승하는 대신 구성하라

#### Rule 17. 계승을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 계승을 금지하라

#### Rule 18. 추상 클래스 대신 인터페이스를 사용하라

#### Rule 19. 인터페이스는 자료형을 정의할 때만 사용하라

> 인터페이스를 구현하는 클래스를 만들게 되면, 그 인터페이스는 해당 클래스의 객체를 참조할 수 있는 자료형(type)역할을 하게 된다.
> 이 기준에 미달하는 사례로는 소위 상수 인터페이스라는 것이 있다. 이런 인터페이스에는 메서드가 없고, static final 필드만 있다. 이런 것은 인터페이스를 잘못 사용한 것이다.
**상수 인터페이스**
```java
public interface PhysicalConstants {
  static final double AVOGADROS_NUMBER = 6.02214199e23;
...
}
```
위는 다음과 같이 유틸리티 클래스를 사용해 바꾸는 것이 바람직하다.
**유틸리티 클래스**
```java
public class PhysicalConstants {
   private PhysicalConstants() {} // 객체 생성을 막음
   static final double AVOGADROS_NUMBER = 6.02214199e23;
...
}
```

#### Rule 20. 태그 달린 클래스 대신 클래스 계층을 활용하라
**태그 달린 클래스의 예제**
```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE };

    // 어떤 모양인지 나타내는 태그 필드
    final Shape shape;

    // 태그가 RECTANGLE 일 때만 사용되는 필드들
    double length, width;

    ...

    // 사각형 생성자
    Figure(double length, double width) {
    ...
    }
}
```
위는 아래와 같이 바꾸는 것이 낫다다
**태그 달린 클래스의 예제**
```java
abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
...
}
```

#### Rule 21. 전략을 표현하고 싶을 때는 함수 객체를 사용하라

#### Rule 22. public class 안에는 public field를 두지 말고 접근자 메서드를 사용하라

<br/>

### 제네릭
제네릭 : 선언부에 형인자가 포함된 클래스나 인터페이스
자바에서는 관습상 형인자를 T가 아니라 E로 쓴다?

#### Rule 23. 새 코드에는 무인자 제네릭 자료형을 사용하지 마라

#### Rule 24. 무점검 경고 (unchecked warning)을 제거하라

#### Rule 25. 배열 대신 리스트를 써라

#### Rule 26. 가능하면 제네릭 자료형으로 만들 것

#### Rule 27. 가능하면 제네릭 메서드로 만들 것

#### Rule 28. 한정적 와일드카드를 써서 API 유연성을 높여라
PECS : (Produce - Exdends, Consumer - Super)

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src)
        push(e);
}
...
public void popAll(Collection<? super E> dst) {
    while (!isEmpty())
        dst.add(pop());
}
```

#### Rule 29. 형 안전 다형성 컨테이너를 쓰면 어떨지 따져보라
```java
// 형 안전 다형성 (heterogeneous) 컨테이너 패턴 - API
public class Favorites {
    public <T> void putFavorite(Class<T> type, T instance);
    public <T> T getFavorite(Class<T> type);
}

// 형 안전 다형성 (heterogeneous) 컨테이너 패턴 - 구현
public class Favorites {
    private Map<Class<?>, Object> favorites = new HashMap<Class<?>, Object>();
    
    public <T> void putFavorite(Class<T> type, T instance) {
        if (type == null) 
            throw new NullPointerException("Type is null");
        favorites.put(type, type.cast(instance));
    }

    public <T> T getFavorite(Class<T> type, T instance) {
        return type.cast(favorites.get(type));
    }
}

// 형 안전 다형성 (heterogeneous) 컨테이너 패턴 - client
public static void main(String[] args) {
    Favorites f = new Favorites();
    f.putFavorite(String.class, "Java");
    f.putFavorite(Integer.class, "0xcafebabe");
    f.putFavorite(Class.class, Favorites.class);

    String favoriteString = f.getFavorite(String.class);
    ...
}
```
이떄 Favorites 객체는 형 안전성을 보장한다. 다시말해, String 을 요청했는데 Integer를 반환한다거나 하지 않는다. 또한 다형성을 갖고 있다.

<br/>

### enum과 어노테이션

#### Rule 30. int 상수 대신 enum을 사용하자

#### Rule 31. ordinal 대신 객체 필드를 사용하라

```java
// ordinal을 남용한 사례 - 따라하면 곤란함
public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET;

    public int numberOfMusicians() { return ordinal() + 1;}
}
```
위의 코드는 다음과 같이 바꾸는 것이 좋다
```java
// ordinal을 남용한 사례 - 따라하면 곤란함
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5);

    private final int numberOfMusicians;
    Ensemble(int size) {this. numberOfMusicians = size;}
    public int numberOfMusicians() { return numberOfMusicians; }
}
```

#### Rule 32. 비트 필드(bit field) 대신 EnumSet을 사용하라

#### Rule 33. ordinal을 배열 첨자로 사용하는 대신 EnumMap을 이용하라

#### Rule 34. 확장 가능한 enum을 만들어야 한다면 인터페이스를 이용하라
















