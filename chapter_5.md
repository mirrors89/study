# 자바 8 인 액션

## \#5 스티림 활용

### 1. 필터링과 슬라이싱
- 프리디케이트로 필터링
```java
// filter 메서드는 피리디케이트를 인수로 받아서 프리디케이트와 일치하는 모든 요소를 포함하는 스트림을 반환한다.
List<Dish> vegetarianMenu = menu.stream()
                                .filter(Dish::isVegetarian)
                                .collect(toList());
```
- 고유 요소로 필터링
```java
// distinct는 고유 요소로 이루어진 스트림을 반환한다.
List<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
numbers.stream()
        .filter(i -> i % 2 == 0)
        .distinct()
        .forEach(System.out::println);
```
- 스트림 축소
```java
// limit
```

- 요소 건너뛰기
