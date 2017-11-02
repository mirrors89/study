# Typescript

## 10. Generic

#### any -> generic
- any는 타입 헬퍼가 재대로 작동 되지 않음
- generic을 이용하면 타입 헬퍼가 정상적으로 작동 한다.
```javascript
// any
function c(message: any): any {
  return message;
}

// generic
function hello<T>(message: T): T {
  return message;
}

helloAny("ks.lee").length;
hello("ks.lee").length;
```

#### Generic Basic
- Generic 타입을 쓰지 않으면, T 로 추론
- Generic 타입을 쓰면, T 를 확인
```javascript
function helloGeneric<T>(message: T): T {
    return message;
}

function hello<T>(message: T): T {
    return message;
}

console.log(hello<string>("Hello"));
let age = hello(29);
hello<number>("29");
```

#### Generic types
- 구현체에 return T 를 설정하지 않아도 오류가 나지 않는다.
```javascript
type HelloGeneric = <T>(message: T) => T;

const hello: HelloGeneric = <T>(message: T): T => {
  return message;
}

console.log(hello<string>("Hello").length);
```

#### Generic class
```javascript
class Person<T> {
  private _name: T;
  private _age: number;

  constructor(name: T) {
    this._name = name;
  }
}

new Person("ks.lee");
// new Person<string>(29);
// 명시적으로 제네릭 타입을 설정하면 오류
```

#### Generic with extends
```javascript
class Person<T extends string | number> {
  private _name: T;
  private _age: number;

  constructor(name: T) {
    this._name = name;
  }
}
```

#### Generic multiple types
```javascript
class Person<T, K> {
  private _name: T;
  private _age: K;

  constructor(name: T, age: K) {
    this._name = name;
    this._age = age;
  }
}

new Person("ks.lee", 29);
```

#### Generic type lookup system
```javascript
interface Person {
  name: string;
  age: number;
}

function getProperty<T, K extends keyof T>(obj T, key: K) {
  return obj[key];
}

function setProperty<T, K extends keyof T>(obj: T, key: K, value: T[K]): void {
  obj[key] = value;
}

const person: Person = {
  name: "ks.lee",
  age: 29
}

getProperty(person, "name");
getProperty(person, "name1"); // name1 key가 없으므로 컴파일 오류 발생
setProperty(person, "age", 30);
```




**PREV > [9. Interface](https://github.com/mirrors89/study/tree/master/typescript/9_Class.md)**

**NEXT > 11. Iterator**
