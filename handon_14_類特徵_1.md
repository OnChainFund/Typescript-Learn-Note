## 類成員可見性
- public: 公開,默認,任何對象在任何地方都可以訪問
- protected: 受保護的,只能在當前類或子類中訪問
- private: 私有的, 只能在當前類中進行訪問

### protected
- protected 的成員只對他們所聲明的類和子類可見
- 子類可以暴露(重設成 public)基類中受保護的成員

範例一
```
class Greeter {
  public greet() {
    console.log(this.getName());
  }

  protected getName() {
    return "Hello World";
  }
}

class SpecialGreeter extends Greeter {
  public hello() {
    console.log(this.getName());
  }
}

const g = new SpecialGreeter();
g.greet();// Hello World
//g.getName();// 報錯：Property 'getName' is protected and only accessible within class 'Greeter' and its subclasses.
g.hello();// Hello World
```

範例二
```
class Base {
  protected m= 100;
}

class Derived extends Base {
  public m = 200;
}

const d = new Derived();
console.log(d.m);// 200
```

### private 
- private 和 protected 一樣,但不允許從子類中訪問該成員
- 跨實例訪問私有屬性

範例一
```
class Base {
  // x 只能在 Base 中被访问
  private x = 3;
}

class Derived extends Base {
  private y = 4;
  showX() {
    //console.log(this.x); // 報錯 Property 'x' is private and only accessible within class 'Base'
  }
}
```

範例二(跨實例調用)
```
class A {
  private x = 10;

  public sameX(other: A) {
    return this.x === other.x;// 可以訪問另一實例的私有屬性
  }
}

const a1 = new A();
const a2 = new A();
console.log(a1.sameX(a2));//true
```

### 靜態成員
不與類的實例相關聯
可以透過類的構造函數本身進行訪問
特殊的靜態名稱不安全,避免使用: name,length,call 等
TS 沒有靜態類的概念,因為有了函數和普通對象
```
class Myclass{
  static a = 10;
  private static b = 20;

  static print(){
    console.log(Myclass.a);
    console.log(Myclass.b);
  }
}

console.log(Myclass.a);// 10
//console.log(Myclass.b);// 報錯：Property 'b' is private and only accessible within class 'Myclass'.
Myclass.print();// 10 20

// 使用子類調用基類的方法
// 說明靜態成員,是會被繼承的
class Base{
  static print(){
    console.log('Base');
  }
}

class Derive extends Base{
  print_plus(){
    Derive.print();
    console.log('Derive');
  }
}

// 特殊的靜態名稱最好不要用
class S {
  //static name = "S"; // 報錯：Static property 'name' conflicts with built-in property 'Function.name' of constructor function 'S'
}

// 在 TS 中沒有靜態類(C#,JAVA有->因為其強致所有數據都必須在類裡面)
// 只有一個實例的類,可以用普通對象來表示
// 可以避免全局污染

class MyStaticClass{
 static doSomething(){}
}

//同上
function doSomething(){}

// 同上
const MyObject = {
  doSomething(){}
}
```

### 類裡的 static 區塊
通過 `#` 定義的靜態成員->私有：
- 可以使用 getter 訪問
- 可以在 static 區塊內訪問

使用情境：
定義成員只會在類的內部使用,且支持類直接調用
```
class Foo {
  static #count = 0;

  get count(){
    return Foo.#count;
  }

  static{
    try{
      const lastInstance={
        length: 0,
      }
      Foo.#count+=lastInstance.length;
    }
    catch{}
  }
}

//console.log(Foo.#count);// 報錯 Property '#count' is not accessible outside class 'Foo' because it has a private identifier.
```
