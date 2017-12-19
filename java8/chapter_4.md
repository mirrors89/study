# 자바 8 인 액션

## \#4 스트림 소개

### 1. 스트림이란 무엇인가?
- 스트림을 이용하면 선영형으로 컬렉션 데이터를 처리할 수 있다.
- 멀티 스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다.


```java
// 자바 7 에서 사용하는 코드
// 1. 400 이하 칼로리의 요리를 필터
List<Dish> lowCaloricDishes = new ArrayList<>();
for(Dish d: menu) {
  if(d.getCalories() < 400) {
    lowCaloricDishes.add(d);
  }
}

// 2. 필터링된 요리를 칼로리 기준으로 정렬
Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
  public int compare(Dish d1, Dish d2) {
    return Integer.compare(d1.getCalories(), d2.getCalories());
  }
});

// 3. 정렬된 요리의 요리명을 반환
List<String> lowCaloricDishesName = new ArrayList<>();
for(Dish d: lowCaloricDishes) {
  lowCaloricDishesName.add(d.getName());
}
```
```java
// 자바 8 에서 사용하는 코드
List<String> lowCaloricDishesName =
                    menu.stream() // stream() 을 parallelStream() 으로 바꾸면 멀티코어 아키텍처에서 병렬로 실행
                        // 1. 400 이하 칼로리의 요리를 필터
                        .filter(d -> d.getCalories() < 400)
                        // 2. 필터링된 요리를 칼로리 기준으로 정렬
                        .sorted(comparing(Dish::getCalories))
                        // 3. 정렬된 요리의 요리명을 반환
                        .map(Dish::getName)
                        .collect(toList());
```
- filter, sorted, map, collect 같은 연산은 고수준 빌딩 블록으로 이루어져 있으므로 모델에 제한되지 않고 자유롭게 어떤 상황에서든 사용할 수 있다.

##### 스트림 API의 특징
- 선언형 : 더 간결하고 가독성이 좋아진다.
- 조립할 수 있음 : 유연성이 좋아진다.
- 병렬화 성능이 좋아진다.

### 2. 스트림 시작하기
- 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소
  - 연속된 요소
    - 컬렉션과 마찬가지로 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공
    - 컬렉션의 주제는 데이터이고 스트림의 주제는 계산
  - 소스
    - 컬렉션, 배열, I/O 자원등의 데이터 제공 소스로부터 데이터를 소비
  - 데이터 처리 연산
    - 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터 베이스와 비슷한 연산을 지원
    - 순차적으로 또는 병렬로 실행 가능
- 파이프라이닝
  - 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록 스트림 자신을 리턴
  - 덕분에 게이름 쇼트서킷 같은 최적화도 얻을 수 있음
- 내부 반복
  - 반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원

```java
List<String> threeHighCaloricDishNames =
                menu.stream()
                    .filter(d -> d.getCalories() > 300)
                    .map(Dish::getName)
                    .limit(3)
                    .collect(toList());
System.out.println(threeHighCaloricDishNames);
```

- filter : 람다를 인수로 받아 스트림에서 특정 요소를 제외 시킨다.
- map : 람다를 이용해서 다른 요소로 변환하거나 정보를 추출한다.
- limit : 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소한다.
- collect : 스트림을 다른형식으로 변환한다.


### 3. 스트림과 컬렉션
- `컬렉션` 모든 값을 메모리에 저장하는 자료구조
- `스트림` 요청할 때만 요소를 계산하는 자료구조

- 딱 한번만 탐색할 수 있다!
  - 반복자와 마찬가지로 스트림도 한번만 탐색
  - 다시 탐색을 하려면 스트림을 다시 만들어야 한다.
  ```java
  List<String> title = Arrays.asList("Java8", "In", "Action");
  Stream<String> s = title.stream();
  s.forEach(System.out::println); // title의 각 단어를 출력
  s.forEach(System.out::println); // 스트림이 이미 소비되었거나 닫힘
  ```
- 외부 반복과 내부 반복
  - `컬랙션` 사용자가 직접 요소를 반복 (외부반복)
  - `스트림` 반복을 알아서 처리하고 결과스트림 값을 어딘가 저장 (내부반복)

### 4. 스트림 연산
- 중간연산
  - 연결할 수 있는 스트림 연산
  - 단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무연산도 수행하지 않음
    - limit 연산 그리고 쇼트 서킷 기법
    - filter와 map은 서로 다른 연산이지만 한과정으로 병합 (루프 퓨전)
- 최종연산
  - 스트림 파이프 라인에서 결과를 도출
  - 스트림 이외의 결과가 반환


### 5. 스트림 이용하기
- 스트림 이용과정 세가지 요약
  - 질의를 수행할 `데이터 소스`
  - 스트림 파이프 라인을 구성할 `중간 연산` 연결
  - 스트림 파이프라인을 실행하고 결과를 만들 `최종 연산`
