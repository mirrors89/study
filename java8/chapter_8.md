# 자바 8 인 액션

## \#8 리펙토링, 테스팅, 디버깅

#### 1. 가독성과 유연성을 개선하는 리펙토링
- 람다 표현식은 익명 클래스보다 코드를 좀더 간결하게 만든다.
- 람다 표현식은 동작파라미터 형식을 지원하므로 더 큰 유연성을 갖출 수 있다.

##### 1.1 코드 가독성 개선
- 가독성이 좋다는 `어떤 코드를 다른 사람도 쉽게 이해 할 수 있다.`를 의미한다.
- 자바 8 코드 가독성에 도움을 주는 기능
  - 코드의 장황함을 줄여서 쉽게 이해할 수 있는 코드를 구현할 수 있다.
  - 메서드 레퍼런스와 스트림 API를 이용해서 코드의 의도를 쉽게 표현할 수 있다.
- 람다, 메서드 레퍼런스, 스트림을 활용해서 코드 가독성을 개선할 수 있는 3가지
  - 익명 클래스를 담다 표현식으로 리팩토링
  - 람다 표현식을 메서드 레퍼런스로 리팩토링
  - 명령형 데이터 처리를 스트림으로 리팩토링

##### 1.2 익명 클래스를 람다 표현식으로 리팩토링
- 하나의 추상 메서드를 구현하는 익명 클래스는 람다 표현식으로 리팩토링할 수있다.

```java
Runnable r1 = new Runnable() {
  public void run() {
    System.out.println("Hello");
  }
};

Runnable r2 = () -> System.out.println("Hello");
```

- 모든 익명 클래스를 람다 표현식으로 변환할 수 있는 것은 아니다.
  - 익명 클래스에서 사용한 `this`와 `super`는 람다 표현식에서 다른 의미를 가진다.
    - 익명클래스에서 `this`는 익명 클래스 자신을 가르치지만 람다에서 `this`는 람다를 감싸는 클래스를 가르킨다.
  - 익명 클래스는 감싸고 있는 클래스의 변수를 가릴 수 있다. 람다 표현식에선 가릴 수 없다.
  - 익명 클래스를 람다 표현식으로 바꾸면 콘텍스트 오버로딩에 따른 모호함이 초래 될 수 있다.

```java
int a = 10;
Runnable r1 = () -> {
  int a = 2;  // <- 컴파일 에러!
  System.out.println(a);
};

Runnable r2 = new Runnable() {
  public void run() {
    int a = 2;
    System.out.println(a);
  }
};
```

```java
interface Task {
  public void execute();
}
public static void doSomething(Runnable r) { r.run(); }
public static void doSomething(Task a) { a.execute(); }

// 익명 클래스 표현식
doSomething(new Task() {
  public void execute() {
    System.out.println("Danger danger!!");
  }
});

// 자바 8 람다 표현식
// Runnable, Task 둘 중 어느것을 가르키는지 알 수 없는 문제가 발생
doSomething(() -> System.out.println("Danger danger!!"));
// 명시적 형변환 (Task)를 이용해서 모호함을 제거할 수 있다.
doSomething((Task)() -> System.out.println("Danger danger!!"));
```

##### 1.3 람다 표현식을 메서드 레퍼런스로 리팩토링
- 람다 표현식 대신 메서드 레퍼런스를 이용하면 가독성을 높일 수 있다.

```java
// 람다 표현식
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel =
  menu.stream().collect(groupingBy(dish -> {
    if(dish.getCalories() <= 400) return CaloricLevel.DIET;
    else if(dish.getCalories() <= 700) return CaloricLevel.NORMAL;
    else return CaloricLevel.FAT;
}));

// 메서드 레퍼런스 방식
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel =
  menu.stream().collect(groupingBy(Dish::getCaloricLevel));

public class Dish {
  ...
  public CaloricLevel getCaloricLevel() {
      if(this.getCalories() <= 400) return CaloricLevel.DIET;
      else if(this.getCalories() <= 700) return CaloricLevel.NORMAL;
      else return CaloricLevel.FAT;
  }
}
```


##### 1.4 명령형 데이터 처리를 스트림으로 리팩토링하기
- 스트림은 쇼트서킷과 게으름이라는 강력한 최적화뿐 아니라 멀티코어 아키텍처를 활용할 수 있는 지름길을 제공한다.

```java
// 자바 7 이전 명령형 코드
List<String> dishNames = new ArrayList<>();
for(Dish dish: menu) {
  if(dish.getCalories() > 300) {
    dishNames.add(dish.getName());
  }
}

// Stream을 이용한 병렬화 처리
menu.parallelStream()
    .filter(d -> d.getCalories() > 300)
    .map(Dish::getName)
    .collect(toList());
```

##### 1.5 코드 유연성 개선
- 함수형 인터페이스 적용

- 조건부 연기 실행
  - 코드에서 객체 상태를 자주 확인하거나, 객체의 일부 메서드를 호출하는 상황 이라면
  - 내부적으로 객체의 상태를 확인한 다음에 메서드를 호출 하도록 구현하는 것이 좋다.

```java
if(logger.isLoggable(Log.FINER)) {
  logger.finer("Problem: " + generateDiagnostic());
}
// logger 상태가 isLoggable이라는 메서드에 의해 클리이언트 코드로 노출 된다.
// 로깅할 때마다 looger 객체의 상태를 매번 확인해야 할까?

// 오버로드한 새로운 interface
public void log(Level level, Supplier<String> msgSupplier);
// 다음 처럼 log 메서드를 호출할 수 있다.
logger.log(Level.FINER, () -> "Problem: " + generateDiagnostic());

// log의 내부 구현 코드다.
public void log(Level level, Supplier<String> msgSupplier) {
  if(logger.isLoggable(level))) {
    log(level, msgSupplier.get())
  }
}
```

- 실행 어라운드
  - 매번 같은 준비, 종료 과정을 반복적으로 수행 하는 코드가 있다면 람다로 변환할 수 있다.
  - 준비, 종료 과정을 처리하는 로직을 재사용 함으로 중복을 제거할 수 있다.

```java
String oneLine = processFile((BuffedReader b) -> b.readLine());
String twoLine = processFile((BuffedReader b) -> b.readLine() + b.readLine());

public static String processFile(BufferedReaderProcessor p) throws IOException {
  try (BufferedReader br = new BuffedReader(new FileReader("java8inaction/chap8/data.txt"))) {
    return p.process(br);
  }
}
public interface BuffedReaderProcessor {
  String process(BufferedReader b) throws IOException;
}
```


#### 2. 람다로 객체지향 디자인 패턴 리펙토링

##### 2.1 전략
- 전략 패턴은 세부분으로 구성
  - 알고리즘을 나타나는 인터페이스 (Strategy 인터페이스)
  - 다양한 알고리즘을 나타내는 한 개 이상의 인터페이스 구현
  - 전략 객체를 사용하는 한개 이상의 클라이언트

```java
public interface ValidationStrategy {
    public boolean execute(String s);
}

public static private class IsAllLowerCase implements ValidationStrategy {
    public boolean execute(String s){
        return s.matches("[a-z]+");
    }
}
public static private class IsNumeric implements ValidationStrategy {
    public boolean execute(String s){
        return s.matches("\\d+");
    }
}

public class Validator{
    private final ValidationStrategy strategy;

    public Validator(ValidationStrategy v){
        this.strategy = v;
    }
    public boolean validate(String s){
        return strategy.execute(s);
    }
}

// 실행부

// 자바 7 이전 전략 패턴 방식
Validator v1 = new Validator(new IsNumeric());
System.out.println(v1.validate("aaaa"));
Validator v2 = new Validator(new IsAllLowerCase ());
System.out.println(v2.validate("bbbb"));


// 람다 표현식 전략 패턴
// Predicate<String> 과 같은 함수 디스크립터를 갖고 있음.
Validator v3 = new Validator((String s) -> s.matches("\\d+"));
System.out.println(v3.validate("aaaa"));
Validator v4 = new Validator((String s) -> s.matches("[a-z]+"));
System.out.println(v4.validate("bbbb"));
```

##### 2.2 템플릿 메서드
- 알고리즘을 제시한 다음 알고리즘의 일부를 고칠 수 있는 유연함을 제공해야할 때 템플릿 메서드 디자인패턴을 이용함.

```java
// 자바 7 이전 템플릿 메서드 방식
// 클래스를 상속을 받아 구현하여 사용할 수 있도록 템플릿을 제공하였다.
abstract class OnlineBanking {
    public void processCustomer(int id){
        Customer c = Database.getCustomerWithId(id);
        makeCustomerHappy(c);
    }
    abstract void makeCustomerHappy(Customer c);
}

```java
// 람다 표현식을 사용한 템플릿 메서드 방식
public class OnlineBankingLambda {
    public void processCustomer(int id, Consumer<Customer> makeCustomerHappy){
        Customer c = Database.getCustomerWithId(id);
        makeCustomerHappy.accept(c);
    }
}

// 클래스를 상속 받지 않고 람다 표현식을 전달해서 다양한 동작을 추가할 수 있다.
public static void main(String[] args) {
    new OnlineBankingLambda().processCustomer(1337, (Customer c) -> System.out.println("Hello!"));
}

```

##### 2.3 옵저버
-


##### 2.4 의무 체인


##### 2.5 팩토리


#### 3. 람다 테스팅
- 람다를 이용한 단위 테스팅

##### 3.1 보이는 람다 표현식의 동작 테스팅
- 람다를 필드에 저장해서 재사용할 수 있으며 람다의 로직을 테스트할 수 있다.
- 람다표현식은 함수형 인터페이스의 인스턴스를 생성한다는 사실을 기억하자.

```java
public class Point {
  ...
  public static final Comparator<Point> compareByXAndThenY =
          comparing(Point::getX).thenComparing(Point::getY);

}

@Test
public void testComparingTwoPoints() throws Exception {
    Point p1 = new Point(10, 15);
    Point p2 = new Point(10, 20);

    int result = Point.compareByXAndThenY.compare(p1, p2);
    assertEquals(-1, result);

}
```

##### 3.2 람다를 사용하는 메서드의 동작에 집중하라
- 람다의 목표는 정해진 동작을 다른 메서드에서 사용할 수 있도록 하나의 조각으로 캡슐화 하는 것이다.
- 세부 구현을 포함하는 람다 표현식을 공개하지 말아야 한다.

```java
public class Point {
  ...
  public static List<Point> moveAllPointsRightBy(List<Point> points, int x) {
      return points.stream().map(p -> new Point(p.getX() + x, p.getY())).collect(toList());
  }
}

@Test
public void testMoveAllPointsRightBy() throws Exception {
    List<Point> points = Arrays.asList(new Point(5,5), new Point(10,5));
    List<Point> expectedPoints = Arrays.asList(new Point(15,5), new Point(20,5));

    List<Point> newPoints = Point.moveAllPointsRightBy(points, 10);

    assertEquals(expectedPoints, newPoints);
}
```

##### 3.3 복잡한 람다를 개별 메서드로 분할하기
- 복잡한 람다 표현식은 테스트 코드에서 메서드 레퍼런스로 바꿔 일반 메서드를 테스트 하듯이 람다 표현식을 테스트할 수 있다.

##### 3.4 고차원 함수 테스팅
- 함수를 인수로 받거나 다른 함수를 반환하는 메서드는 사용하기 어렵다.
- 메서드가 람다를 인수로 받는다면 다른 람다로 메서드의 동작을 테스트할 수 있다.

```java
@Test
public void testFilter() throws Exception {
    List<Integer> numbers = Arrays.asList(1,2,3,4);
    List<Integer> even = filter(numbers, i -> i % 2 == 0);
    List<Integer> smallerThenThree = filter(numbers, i -> i < 3);

    assertEquals(Arrays.asList(2, 4), even);
    assertEquals(Arrays.asList(1, 2), smallerThenThree);
}
```

#### 4. 디버깅
