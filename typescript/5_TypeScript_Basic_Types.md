# Typescript

## 5. TypeScript Basic Types

#### TypeScript에서 프로그램 작성을 위해 기본 제공하는 데이터 타입
- ECMAScript 표준 기본 자료형
  - Boolean
  - Number
  - String
  - Null
  - Undefined
  - Symbol (ECMAScript 6에 추가)
  - Array : object 형
- 프로그래밍을 도울 몇가지 타입이 더 제공
  - Any
  - Void
  - Never
  - Enum
  - Tuple : object 형

#### Primitive Type
- 오브젝트와 레퍼런스 형태가 아닌 실제 값을 저장하는 자료형
- Primitive Type의 내장 함수를 사용 가능한 것은 자바스크립트 처리 형식 덕분
```javascript
let name = "mark";
name.toString();
```

#### literal
- 값자체가 변하지 않는 값

#### Boolean / boolean
- true / false 값을 가짐

#### Number / number
- 모든 숫자는 부동 소수점 값

#### String / string


#### Template String
- \`\`

#### undefined & null
- `undefined` 와 `null` 은 실제로 각각 고유한 타입을 가짐
- `void`와 마찬가지로 `undefined`와 `null`은 그자체로는 쓸모가 없음
- `undefined` & `null`은 모든 타입의 서브 타입이다.
  - 컴파일 옵션에서 `--strictNullChecks` 사용하면 `null`과 `undefined`는 `void`나 자기 자신들에게만 할당 할 수 있다.
  - 이경우, `null`과 `undefined`를 할당할 수 있게 하려면, `union type`을 이용해야 한다.

#### void
- 타입이 없는 상태
- `any`와 반대의 의미를 가짐
- 주로 함수의 리턴이 없을때 사용
```javascript
function returnVoid(message): void {
  console.log(message);
}
returnVoid("리턴이 없음");
```

#### any
- 어떤 타입이어도 상관없는 타입
- 이걸 최대한 사용하지 않는게 핵심
- 컴파일 옵션 중에는 any를 쓰면 오류를 뱉도록 하는 옵션도 있음
  - noImplicitAny
```javascript
function returnAny(message): any {
    console.log(message);
}
returnAny("리턴은 아무거나");
```

#### never
- 리턴에 주로 사용
```javascript
function error(message : string) : never {
  throw new Error(message);
}

function fail() : never {
  return error("Something Failed");
}

function infiniteLoop() : never {
  while(true){
  }
}
```

#### Array
- javascript array는 객체
- 사용 방법
  - Array<타입>
  - 타입[]

#### Tuple
- 배열인데 타입이 한가지가 아닐경우

#### Enum
- C enum과 동일함
```javascript
enum Color {Red, Green=1, Blue}
let c:Color = Color.Green;
enum Color {Red, Green, Blue}
let colorName:string = Color[2];
```

#### Symbol
- ECMAScript 2015의 Symbol
- 고유하고 수정 불가능한 값으로 만들어줌
- 접근을 제어하는 경우에 사용됨
```javascript
let sym = Symbol();
let obj = {
  [sym]: "value"
};
console.log(obj[sym]); // value
```

**PREV > [4. Compiler Options](https://github.com/mirrors89/study/tree/master/typescript/4_Compiler_Options.md)**

**NEXT > [6. var, let, const](https://github.com/mirrors89/study/tree/master/typescript/6_var_let_const.md)**
