# 자바 8 인 액션

## \#2 동작 파라미터화 코드 전달하기

1. 동작 파라미터화 (behavior parameterization)
 - 어떻게 실행할 것인지 결정하지 않은 코드 블록
 - 자주 바뀌는 요구사항에 효과적으로 대응 할 수 있다.

2. 변화하는 요구사항에 대응 하기

- 첫번째 시도 : 녹색사과 필터링

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if("green".equals(apple.getColor())) {
            result.add(apple);
        }
    }

    return result;
}
```

 - 두번째 시도 : 색을 파라미터화

```java
// 색을 파라미터화
public static List<Apple> filterApplesByColor(List<Apple> inventory, String color) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(color.equals(apple.getColor())) {
            result.add(apple);
        }
    }

    return result;
}
```

```java
// 무게도 파라미터화 해서 만든다.
// 좋은 해결법이지만 DRY(Don't repeat yourself) 원칙에 어긋난다.
public static List<Apple> filterApplesByWeight(List<Apple> inventory, Integer weight) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(weight < apple.getWeight()) {
            result.add(apple);
        }
    }

    return result;
}
```
- 세번째 시도 가능한 모든 속성으로 필터링

```java
// 정말 좋지 않은 코드
// 코드를 처음 봤을때 flag가 무엇을 뜻하는지 모른다.
public static List<Apple> filterApple(List<Apple> inventory, Integer weight, String color, boolean flag) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(flag && weight < apple.getWeight() || !flag && color.equals(apple.getColor())) {
            result.add(apple);
        }
    }

    return result;
}
```

- 네번째 시도 추상적 조건으로 필터링


```java
// ApplePred
public interface ApplePred {
    boolean test (Apple apple);
}

// AppleGreenColorPredicate
public class AppleGreenColorPredicate implements ApplePred {
    @Override
    public boolean test(Apple apple) {
        return apple.getColor().equals("green");
    }
}

// AppleHeavyWeightPredicate
public class AppleHeavyWeightPredicate implements ApplePred {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
}

// filterAppleWithPredicate
// 인터페이스를 이용한 전략패턴
// 자바 7 이하 버전에서 많이 쓰이는 동작 파라미터화
public static List<Apple> filterAppleWithPredicate(List<Apple> inventory, ApplePred p) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(p.test(apple)) {
            result.add(apple);
        }
    }

    return result;
}
```

- 다섯번째 시도 익명 클래스 사용


 ```java
// 반복되는 코드가 발생한다.
// 익명클래스의 사용이 익숙치 않아 코드 가독성에 문제가 생긴다.
List<Apple> greenApples = FilterApple.filterAppleWithPredicate(list, new ApplePred() {
    @Override
    public boolean test(Apple apple) {
        return apple.getColor().equals("green");
    }
});

List<Apple> heavyApples = FilterApple.filterAppleWithPredicate(list, new ApplePred() {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
});
 ```


- 여섯번째 시도 람다 표현식 사용

```java
// 익명클래스를 람다 표현식으로 수정하면 코드가 간결해진다.
List<Apple> greenApples = FilterApple.filterAppleWithPredicate(list, (Apple apple) -> apple.getColor().equals("green"));
List<Apple> heavyApples = FilterApple.filterAppleWithPredicate(list, (Apple apple) -> apple.getWeight() > 150);
```

- 일곱번째 시도 리스트 형식으로 추상화

```java
// 메소드에서 타입도 추상화 한다.
public interface Predicate<T> {
    boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
  List<T> result = new ArrayList<>();
  for(T e : list) {
      if(p.test(e)) {
          result.add(e);
      }
  }

  return result;
}
```
