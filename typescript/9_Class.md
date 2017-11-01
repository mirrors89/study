# Typescript

## 9.Class

#### 클래스 생성
```javascript
class Person {
  name: string = null;
  age: number = null;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const person: Person = new Person("ks.lee", 29);
console.log(person);
```

#### 클래스 프로퍼티 접근 제어자
- private
```javascript
class Person {
  public name: string = null;
  private _age: number = null;

  constructor(age: number) {
    this._age = age;
  }
}

const person: Person = new Person(29);
person.name = "ks.lee" // 접근가능
// person._age 접근 불가능
console.log(person);
```
- protected

```javascript
class Person {
  protected _name: string = null;
  private _age: number = null;
}

// Person 상속
class Child extends Person {
  constructor(name: string) {
    super();
    this._name = name;
  }
}

const person: Child = new Child("ks.lee");
console.log(person);
```


#### 클래스와 메서드
```javascript
class Person {
  constructor(protected _name: string, protected _age: number) {
  }
  // hello 메서드
  hello () {
    console.log(this._name);
  }
}

const person: Person = new Person("ks.lee", 29);
person.hello();
```

#### 클래스 상속
```javascript
class Person {
  constructor(protected _name: string, protected _age: number) {
  }
  // hello 메서드
  hello () {
    console.log(this._name);
  }
}

const person: Person = new Person("ks.lee", 29);
person.hello();

// default 생성자는 부모의 생성자
class Child extends Person {
  _name: string = "ks.lee .Jr"
}

const child: Child = new Child("", 10);
child.hello();
```

#### 클래스와 getter, setter
```javascript
class Person {
  private _name: string;
  private _age: number;

  constructor(name: string, age:number) {
    this._name = name;
    this._age = age;
  }

  get name(): string {
    return this._name;
  }

  set name(name: string) {
    this._name = name;
  }

  toString() {
    console.log(`Person [_name=${this._name}, _age=${this._age}]`);
  }
}

const person: Person = new Person("ks.lee", 29);
console.log(person.name);
person.name = "new ks.lee";
person.toString();
```

#### Abstract Class
```javascript
abstract class APerson {
  protected _name: string;
  abstract setName(name: string): void;
}

class Person extends APerson {
  setName(name: string): void {
    this._name = name;
  }
}

const person: Person = new Person();

```

#### 클래스와 private constructor
```javascript
class Preference {
  // 생성자 함수 앞에 접근제어자인 private를 붙일 수 있음.
  // 외부에서 생성이 불가능
  private constructor() {
  }
}
```

#### 클래스와 싱글턴 패턴
```javascript
class Preference {
  private static Instance: Preference = null;
  public static getInstance)(): Preference {
    if(Preference.Instance === null) {
      Preference.Instance = new Preference();
    }
    return Preference.Instance;
  }

  private constructor() {    
  }

  hello() {
    console.log("Hello!");
  }
}

const p = Preference.getInstance();
p.hello();
```

#### 클래스와 readonly
- private readonly로 선언된 경우, 생성자에서는 할당이 가능
- public readonly로 선언된 경우, 클래스 외부에서는 다른값을 할당할 수 없음
```javascript
class Person {
  private readonly _name: string;
  public readonly _age: number;

  constructor(name: string, age:number) {
    this._name = name;
    this._age = age;
  }

  get name(): string {
    return this._name;
  }

  set name(name: string) {
    // private readonly 이므로 할당 불가능.
    //this._name = name; (x)
  }

  toString() {
    console.log(`Person [_name=${this._name}, _age=${this._age}]`);
  }
}

const person: Person = new Person("ks.lee", 29);
console.log(person.name);
// public readonly 이므로 외부에서 할당 불가능.
//person.age = 29; (x)
person.toString();
```


**PREV > [8. Interface](https://github.com/mirrors89/study/tree/master/typescript/8_Interface.md)**

**NEXT > 10. Generic**
