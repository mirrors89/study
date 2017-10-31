# Typescript

## 7. Type assertions, Type alias

#### Type assertions
- `타입이 이것이다.`라고 컴파일러에게 알려주는 것을 의미
- 형변환과는 다름
- 두가지 방법이 있음
  - 변수 as 강제할타입
  - <강제할타입>변수
```javascript
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
let strLength: number = (someValue as string).length;

// 주로 넓은 타입에서 좁은 타입으로 강제할 경우가 많다.
// jsx 에서는 as를 사용한다.
```

#### Type alias
- 인터페이스와 비슷해 보임
- Primitive, Union Type, Tuple
- 기타 직접 작성해야 하는 타입을 다른 이름으로 지정할 수 있음
- 타입을 만드는것은 아님

```javascript
// Union Type alias
// let a: string | number; // 타입이 string도 되고 number도 될 수 있다.
type StringOrNumber = string | number
let a: StringOrNumber

a = '스트링';
a = 0;
```
```javascript
// Tuple
let person: [string, number] = ['Mark', 35];

// Tuple alias
type PersonTuple = [string, number];
let another: PersonTuple = ['Anna', 24];
```

#### alias와 Interface와 다른점




**PREV > [6. var, let, const](https://github.com/mirrors89/study/tree/master/typescript/6_var_let_const.md)**

**NEXT > 8. Interface**
