# 자바 8 인 액션

## \#1 자바 8을 눈여겨 봐야하는 이유


#### 자바 8의 특징
- 간결한 코드
- 멀티코어 프로세서의 간단한 활용

###### 자바 8 이전의 코드
```java
Collections.sort(inventory, new Comparator<Apple>() {
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight().compareTo(a2.getWeight());
  }
});
```

###### 자바 8 코드
```java
  inventory.sort(comparing(Apple::getWeight));
```



병렬 연산을 지원하는 스트림 API 등장
메서드에 코드를 전달하는 간결기법
인터페이스의 디폴트 메서드
