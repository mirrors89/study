# 자바 8 인 액션

## \#7 병렬 데이터 처리와 성능

- 자바 7에서 병렬 처리를 하기 위해선 스레드를 이용해야 했지만 오류가 많이 발생하고 동기화도 신경 써줘야한다.
- 자바 7에서 병렬화 수행하면서 에러를 최소화 하기 위해서 포크/조인 프레임 워크를 제공한다.
- 스트림으로 데이터 컬렉션 관련 동작을 쉽게 병렬로 실행할 수 있다.

#### 1. 병렬 스트림
- 컬렉션에 parallelStream을 호출하면 병렬 스트림이 생성된다.
- 병렬 스트림은 각각의 스레드에서 처리할 수 있도록 스트림 요소를 청크로 분할한 스트림이다.

```java
// 1부터 n까지의 모든 숫자의 합계를 반환하는 메서드
public static long sequntialSum(long n) {
  return Stream.iterate(1L, i -> i + 1)
                .limit(n)
                .reduce(0L, Long::sum); // BinaryOperator로 리듀싱
}
```

```java
// java 7 이전 스타일
public static long iteraticeSum(long n) {
  long result = 0;
  for (long i = 1L; i <= n; i++) {
    result += i;
  }

  return result;
}
```
- n이 커진다면 연산을 병렬로 처리하는 것이 좋을 것이다.

##### 1.1 순차 스트림을 병렬 스트림으로 변환하기
- 순차 스트림에 `parallel()` 메서드를 호출하면 기존의 함수형 리듀싱 연산이 병렬로 처리

```java
public static long parallelSum(long n) {
    return Stream.iterate(1L, i -> i + 1)
                .limit(n)
                .parallel()
                .reduce(0L, Long::sum);
}
```

- 병렬 스트림에 `sequntial()` 메서드를 이용해 순차 스트림으로 바꿀 수 있다.
- `parallel()`과 `sequntial()` 메서드를 이용해서 어떤 연산은 병렬로 어떤 연산은 순차로 실행하도록 제어할 수 있다.

##### 1.2 스트림 성능 측정
- 병렬화를 하면 순차나 반복 형식에 비해 성능이 좋아질꺼라 추축했다.

```java
// n개의 숫자를 더하는 함수의 성능 측정
public long measureSumPerf(Function<Long, Long> adder, long n) {
  long fastest = Long.MAX_VALUE;
  for (int i = 0; i < 10; i++) {
    long start = System.nanoTime();
    long sum = adder.apply(n);
    long duration = (System.nanoTime() - start) / 1_000_000;
    System.out.println("Result : " + sum);
    if (duration < fastest) fastest = duration;
  }
}

public static void main(String[] arg) {
  // Sequential sum done is : 1355 msecs
  System.out.println("Sequential sum done is : " + measureSumPerf(ParallelStreams::sequentialSum, 100_000_000) + " msecs");
  // Iterative sum done is : 34 msecs
  System.out.println("Iterative sum done is : " + measureSumPerf(ParallelStreams::iterativeSum, 100_000_000) + " msecs");
  // Parallel sum done is : 7874 msecs
  System.out.println("Parallel sum done is : " + measureSumPerf(ParallelStreams::parallelSum, 100_000_000) + " msecs");
}
```
- 하지만 병렬버전이 느리게 동작한다.. 왜일까?
  - for 루프는 저수준으로 동작하며 기본 값을 박싱, 언박싱할 필요가 없어 수행속도가 빠르다.
  - iterate가 박싱된 객체를 생상하므로 언박싱하는 과정이 필요했다.
  - iterate는 병렬로 실행될 수 있도록 독립적인 정크로 분할 하기 어렵다.

##### 더 특화된 메서드
- 멀티코어 프로세서를 활용해서 효과적으로 합계 연산을 병렬로 실행하려면 어떻게 해야할까?
  - LongStream.rangeClosed는 long을 직접 사용하므로 박싱과 언박싱 오버헤드가 사라진다.
  - LongStream.rangeClosed는 쉽게 청크로 분할할 수 있는 순자 범위를 생산한다.

```java
public static long rangedSum(long n) {
    return LongStream.rangeClosed(1, n).reduce(Long::sum).getAsLong();
}

public static long parallelRangedSum(long n) {
    return LongStream.rangeClosed(1, n).parallel().reduce(Long::sum).getAsLong();
}

// Ranged sum done in : 12 msecs
System.out.println("Ranged sum done in : " + measureSumPerf(ParallelStreams::rangedSum, 10_000_000));
// ParallelRangedSum sum done in : 3 msecs
System.out.println("Parallel ranged sum done in : " + measureSumPerf(ParallelStreams::parallelRangedSum, 10_000_000));
```
- 상황에 따라서는 어떤 알고리즘을 병렬화하는 것보다, 적절한 자료구조를 선택하는 것이 더 중요하다는 사실을 단적으로 보여준다.
- 병렬화를 이용하려면 스트림을 재귀적으로 분할 해야하고 각 서브 스트림을 서로 다른 스레드의 리듀싱 연산으로 할당하고, 이들 결과를 하나의 값으로 합쳐야 한다.

##### 1.3 병렬 스트림의 올바른 사용법
