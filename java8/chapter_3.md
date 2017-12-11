# 자바 8 인 액션

## \#3 람다 표현식

#### 1. 람다란 무엇인가?
  - 람다 표현식 특징
    - 익명 : 보통의 메서드와 달리 이름이 없음.
    - 함수 : 메서드처럼 특정 클래스에 종속되지 않음
    - 전달 : 메서드 인수로 전달하거나 변수로 저장할 수 있다.
    - 간결성 : 자잘한 코드를 구현할 필요가 없다.

  ```java
  // 파라미터 리스트      화살표             람다 바디
  (Apple a1, Apple a2) -> a1.getWight().compareTo(a2.getWight);
  (parameters) -> expression
  (parameters) -> { statements; }
  ```

#### 2. 어디에, 어떻게 람다를 사용할까?
  - 함수형 인터페이스
    - 하나의 추상 메서드를 지정하는 인터페이스
    - 전체 표현식을 함수형 인터페이스의 인스턴스로 취급
  - 함수 디스크립터
    - 람다 표현식의 시그니처를 서술하는 메서드
    - () -> void
  - 언어를 더 복잡하게 만들지 않기 위해 하나의 추상 메서드를 갖는 인터페이스만 람다를 사용할 수 있도록 설계 하였다.
  - @FunctionalInterface

#### 3. 람다 활용 : 실행 어라운드 패턴
  - 실제 자원을 처리하는 코드를 설정과 정리 두과정이 둘러싸는 형태

  ```java
  public static String processFile() throws IOException {
      try (BufferedReader br =
                    new BufferedReader(new FileReader("data.txt"))) {
          return br.readLine();
      }
  }
  ```

  - 1단계 동작 파라미터화를 기억하라
    - 기존의 설정, 정리과정은 재사용하고 processFile 메서드만 다른 동작을 수행하도록 명령

  ```java
  String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
  ```
  - 2단계 함수형 인터페이스를 이용해서 동작 전달
    - 함수형 인터페이스 자리에 람다를 사용가능
    - BufferedReader -> String 과 IOException을 던질 수 있는 시그니쳐와 함수형 인터페이스를 만들어야 한다.

  ```java
  @FunctionalInterface
  public interface BufferedReaderProcessor {
      String process(BufferedReader br) throws IOException;
  }

  public static String processFile(BufferedReaderProcessor p) throws IOException {
    ...
  }
  ```

  - 3단계 동작 실행
    - 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리

  ```java
  public static String processFile(BufferedReaderProcessor p) throws IOException {
      try(BufferedReader br =
                  new BufferedReader(new FileReader("data.txt"))) {
          return p.process(br);
      }
  }
  ```

  - 4단계 람다 전달

  ```java
String oneLine = processFile((BufferedReader br) -> br.readLine());
String twoLine = processFile((BufferedReader br) -> br.readLine() + br.readLine());
  ```

#### 4. 함수형 인터페이스 사용
  - 함수 디스크립터(function descriptor)
  - Predicate
    - test라는 추상 메서드를 정의하며, 제네릭 형식 T를 객체를 인수로 받아 boolean을 반환한다.

  ```java
  @FunctionalInterface
  public interface Predicate<T> {
    boolean test(T t);
  }
  ```
  - Consumer
    - accept라는 추상 메서드를 정의하며, 제네릭 형식 T 객체를 받아서 void를 반환한다.

  ```java
  @FunctionalInterface
  public interface Consumer<T> {
    void accept(T t);
  }
  ```
  - Function
  - apply라는 추상 메서드를 정의하며, 제네릭 형식 T 객체를 받아서 제네릭 형식 R을 반환한다.

  ```java
  @FunctionalInterface
  public interface Function<T, R> {
    R apply(T t);
  }
  ```

  - 제네릭 파라미터는 참조형만 사용가능

#### 5. 형식 검사, 형식 추론, 제약
  - 형식 검사
    - 콘텍스트에서 기대되는 람다 표현식의 형식을 대상형식(target type) 이라고 한다.
  ```java
  // 람다 표현식의 재구성
  // 람다가 사용된 컨텍스트는 무엇인가?
  filter(inventory, (Apple a) -> a.getWight() > 150);

  // 대상형식은 Predicate<Apple>이다.
  filter(inventory, Predicate<Apple> p);

  // Predicate<Apple>인터페이스의 추상 메서드는 무엇인가?
  public interface Predicate<T> {
    boolean test(T t);
  }

  // Apple 을 인수로 받아 boolean을 반환하는 test 메서드다.
  (Apple) -> boolean

  // 함수 디스크립터는 (Apple) -> boolean 이므로 람다의 시그니처와 일치
  // 형식 검사가 성공적으로 완료된다.
  (Apple a) -> a.getWight() > 150)  ==  (Apple) -> boolean
  ```

  - 같은 람다, 다른 함수형 인터페이스
    - 대상형식이라는 특징 때문에 같은 람다 형식이라도 추상 메서드를 가진 다른 함수형 인터페이스로 사용될 수 있다.
  ```java
  Comparator<Apple> c1 = (Apple a1, Apple a2) -> a1.getWight().compareTo(a2.getWight());
  ToIntBiFunction<Apple, Apple> c2 = (Apple a1, Apple a2) -> a1.getWight().compareTo(a2.getWight());
  BiFunction<Apple, Apple, Integer> c3 = (Apple a1, Apple a2) -> a1.getWight().compareTo(a2.getWight());
  ```

  - 형식 추론
    - 대상형식을 이용해서 함수 디스크립터를 알 수 있으므로 람다의 시그니처도 추론할 수 있다.
    - 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략 가능하다.
  ```java
  List<Apple> greenApples = filter(inventory, a -> "green".equals(a.getColor()));
  List<Apple> greenApples = filter(inventory, (Apple a) -> "green".equals(a.getColor()));
  ```

  - 지역변수 사용
    - 자유변수(외부에서 정의된 변수)를 활용할 수 있다.
    - 이와 같은 동작을 람다 캡처링
    - 람다 표현식은 한 번만 할당할 수 있는 변수를 캡처할 수 있다.
  ```java
  int portNumber = 1337;
  Runnable r = () -> System.out.println(portNumber);

  // 컴파일할 수 없는 코드
  // 지역변수(자유변수)는 final로 선언되거나 실질적으로 final처럼 취급해야한다.
  portNumber = 31337;
  ```
#### 6. 메서드 레퍼런스
  - 특정 메서드만 호출하는 람다의 축약형
  - 명시적으로 메서드명을 참조함으로써 가독성을 높일 수 있다.
  - 메서드 명 앞에 구분자(::)를 붙이는 방식

```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
inventory.sort(comparing(Apple::getWeight));
```

  - 메서드 레퍼런스를 만드는방법
    -

  - 생성자 레퍼런스


#### 7. 람다, 메서드 레퍼런스 활용하기

#### 8. 람다 표현식을 조합할 수 있는 유용한 메서드
