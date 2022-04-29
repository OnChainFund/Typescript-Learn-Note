## 類繼承

### implements 
implements實現接口繼承

範例一
```
interface Pingable {
  ping(): void;
}

class Sonar implements Pingable {
  ping() {
    console.log("pong");
  }
}

class Ball implements Pingable {
  ping() {
    console.log("bong");
  }
}
```

子類型必須實現所有期繼承的接口屬性
但接口並不能約束繼承其類的參數類型(只要兼容就可以)
範例如下:
```
interface Checkable {
  check(name: string): boolean;
}

class NameChecker implements Checkable{
  check(name: string): boolean {//必須顯式聲明 name 是 string
    return name.length > 3;
  }
}
```

可選屬性
```
interface A {
  x: number;
  y?: number;
}

class C implements A{
  x = 1;
}

const c = new C();
console.log(c.x);// 1
```

### extends 子句
extends 實現類繼承

```
class Animal {
  constructor(public name: string) {
    this.name = name;
  }
  move(){
    console.log(`${this.name} is moving`);
  }
}

class Dog extends Animal {
  bark() {
    console.log(`${this.name} is barking`);
  }
}

const dog = new Dog('dog');
dog.bark();// dog is barking
dog.move();// dog is moving
```

### 重寫方法
子類改寫父類的屬性(super.)
```
class Base {
  greet() {
    console.log("Hello World");
  }
}

class Derive extends Base {
  greet(name?: string) {
    if (name) {
      console.log(`Hello ${name}`);
    } else {
      super.greet();
    }
  }
}

const d = new Derive();
d.greet();// Hello World
d.greet("John"); // Hello John

const b: Base =d// 子類是父類的子對象
```

### 初始化順序
運行順序：
1. 基類的字段初始化
2. 基類的構造函數運行
3. 子類的字段初始化
4. 子類的構造函數運行

```
class Base {
  public name="base";
  constructor() {
    console.log("base constructor name:", this.name);
  }
}

class Derived extends Base {
  public name = "derived";
  constructor() {
    super()
    console.log("derived constructor name:", this.name);
  }
}

const d = new Derived();
// base constructor name: base
// derived constructor name: derived
```

### 繼承內置類型
```
class MsgError extends Error {
  constructor(message: string) {
    super(message);
  }
  sayHello() {
    console.log("Hello ", this.message);
  }
}

const msg = new MsgError("World");
console.log(msg.sayHello());
// Hello  World
// undefined
// 以上在低版本的浏览器中可能会报错
// 解法：明確設置原型

class MsgError2 extends Error {
  constructor(message: string) {
    super(message);

    // 明確設置原型
    Object.setPrototypeOf(this, MsgError2.prototype);
  }
  sayHello() {
    console.log("Hello ", this.message);
  }
}
```