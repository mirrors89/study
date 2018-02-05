# 자바 8 인 액션

## \#9. 디폴트 메서드
자바 8에서는 기본 구현을 포함하는 인터페이스를 정의하는 두가지 방법을 제공한다.
 - 정적 메서드
 - 디폴트 메서드

#### 1. 변화하는 API
##### 1.1 API 버전 1
```java
public interface Resizable extends Drawable {
    int getWidth();
    int getHeight();
    void setWidth(int width);
    void setHeight(int height);
    void setAbsoluteSize(int width, int height);
}
```

- Resizable 사용자 구현
```java
public class Ellipse implements Resizable {
  ...
}
```

- Resizable 모양을 처리하는 게임을 만들었다.
```java
public class Game {
    public static void main(String[] args) {
        List<Resizable> resizableShapes =
                Arrays.asList(new Square(), new Rectangle(), new Ellipse());
        Utils.paint(resizableShapes);
    }
}
```
```java
public class Utils{
    public static void paint(List<Resizable> l){
        l.forEach(r -> {
          r.setAbsoluteSize(42, 42);
          r.draw();
        });
    }
}
```

##### 1.2 API 버전 2

- Resizable을 구현하는 Square, Rectangle 구현을 개선해달라는 요청을 받았다.

```java
// 인터페이스를 바꾼다음 컴파일 하면 에러가 발생한다.
public interface Resizable extends Drawable {
    int getWidth();
    int getHeight();
    void setWidth(int width);
    void setHeight(int height);
    void setAbsoluteSize(int width, int height);
    void setRelativeSize(int wFactor, int hFactor);
}
```
- Resizable을 구현하는 모든 클래스는 setRelativeSize 메서드는 구현해야하는 문제가 발생한다.
- 인터페이스에 새로운 메서드를 추가하면 바이너리 호환성은 유지된다.
- 바이너리 호환성이란 새로 추가된 메서드를 호출하지만 않는다면 새로운 메서드 구현이 없이도 기존 클래스 파일 구현이 잘동작한다는 의미


- 자신만의 API를 별도로 만든 다음 예전 버전과 새로운 버전을 직접 관리하는 방법이 있다. 하지만 여러가지 불편하다.
  - 라이브러리 관리가 복잡하다.
  - 사용자는 같은 코드에 예전버전과 새로운 버전 두가지 라이브러리를 모두 사용해야 하는 상황이 생긴다.
  - 프로젝트에 로딩해야 할 클래스 파일이 많아지면서 메모리 사용과 로딩 시간 문제가 발생한다.

- 소스 호환성 : 코드를 고쳐도 기존 프로그램을 성공적으로 재컴파일할 수 있음을 의미
- 동작 호환성 : 코드를 바꾼 다음에도 같은 입력값이 주어지면 프로그램이 같은 동작을 실행 한다는 의미

#### 2. 디폴트 메서드란 무엇인가?
- 디폴트 메서드는 `default`라는 키워드로 시작하며 다른 클래스에 선언된 메서드처럼 메서드 바디를 포함한다.
```java
public interface Sized {
  int size();
  // 디폴트 메서드
  default boolean isEmpty() {
    return size() == 0;
  }
}
```
- 디폴트 메서드는 추상 메서드에 해당하지 않는다.


#### 3. 디폴트 메서드 활용 패턴
