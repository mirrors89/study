# Typescript

## 6. var, let, const

#### var
- ES5
- 변수의 유효범위 : 함수 스코프
- 호이스팅 O
- 재선언 가능

#### let, const
- ES6
- 변수의 유효범위 : 블록 스코프
- 호이스팅 X
- 재선언 불가


#### let, const 타입 추론
```javascript
// a, b, c 는 string 타입, d는 리터럴 타입 "디이"
let a: string = "에이";
let b = "비이";
const c: string = "씨이";
const d = "디이";
```

**PREV > [5. TypeScript Basic Types](https://github.com/mirrors89/study/tree/master/typescript/5_TypeScript_Basic_Types.md)**

**NEXT > 7. Type assertions, Type alias**
