# Typescript

## 7. Interface

#### Interface Basic
```javascript
interface Person {
  name: string;
  age: number;
}

function hello(person: Person): void {
  console.log(`안녕하세요. ${person.name}입니다.`);
}

const person: Person = {
  name: 'ks.lee',
  age: 35
};

hello(person); // 안녕하세요. ks.lee입니다.
```

#### Optional Properties
```javascript
interface Person {
  name: string;
  age?: number; // ? : 있어도 되고 없어도 되는 프로퍼티
}

const person: Person = {
  name: 'ks.lee'
  // age가 없어도 에러가 발생하지 않는다.
}
```

#### Indexable Types Properties
```javascript
interface Person {
  name: string;
  [index: string]: string; // [(명명):(number or string)]: (타입);
}

const person: Person = {
  name: "ks.lee"
}

person.anybody = "Anny";
console.log(person);
```

#### function in interface
```javascript
interface Person {
  name: string;
  hello(): string;
}

const person: Person = {
  name: "ks.lee",
  hello(): string {
    return "Hello";
  }
}

person.hello();
```

#### class implements interface
```javascript
interface IPerson {
  name: string;
  hello(): void;
}

class Person implements IPerson {
  name: string = null;

  constructor(name: string) {
    this.name = name;
  }

  hello(): void {
    console.log(`안녕하세요. ${this.name}입니다.`)
  }
}

const person: IPerson = new Person("ks.lee");
person.hello();
```
#### interface extends interface

```javascript
interface Person {
  name: string;
  age?: number
}

// 인터페이스는 인터페이스를 상속을 받을 수 있음
interface Korean extends Person {
  city: string
}

const k: Korean = {
  name: 'ks.lee',
  city: 'seoul'
}
```

#### function interface
```javascript
interface HelloPerson{
  (name: string, age?: number): void;
}

let helloPerson: HelloPerson = function(name: string){
  console.log(name);
}

helloPerson("Mark");
```


**PREV > [7. Type assertions, Type alias](https://github.com/mirrors89/study/tree/master/typescript/7_Type_assertions_Type_alias.md)**

**NEXT > [9. Class](https://github.com/mirrors89/study/tree/master/typescript/9_Class.md)**
