## 泛型類
```
class Box<Type> {
  contents: Type;

  constructor(contents: Type) {
    this.contents = contents;
  }

  // 靜態成員不能引用類泛型參數
  //static defaultValue: Type; // 報錯 Static members cannot reference class type parameters.
}

const b = new Box("hello");
// 也可以顯示聲明類型

const b_1:Box<string> = new Box("hello");
const b_2 = new Box<string>("hello");
```

## 類運行時中的 this
問題：(沒有辦法正確調用 this)
```
class MyClass {
  name = "My class";
  getName(){
    return this.name
  }
}
const c = new MyClass();
console.log(c.getName());// My class
const obj = {
  name: "new object",
  getName: c.getName
}

console.log(obj.getName());// new object
// 問題點：上面這個應該要打印出 My class 但實際上卻打印出 new object
```
大神也說js 的 this太容易變動了,
解決方法：
- 箭頭函數
- this 參數

箭頭函數：
```
class MyClass {
  name = "My class"
  getName =() =>{
    return this.name
  }
}

const c = new MyClass();
console.log(c.getName());// My class

const obj = {
  name: "new object",
  getName: c.getName
}

console.log(obj.getName());// My class
```
注意：箭頭函數的 this 指的是 MyClass 並不是 MyClass 中的
使用箭頭函數會浪費內存(因為每個類實例都有它的副本,每個函數都是這樣定義的)
使用箭頭函數不能在子類中使用 super.getName()->因為在原型鏈中沒有可以獲得基類的方法

this 參數：
```
class MyClass {
  name = "My class"
  getName (this: MyClass) {
    return this.name
  }
}
const c = new MyClass();
console.log(c.getName());// My class
const obj = {
  name: "new object",
  getName: c.getName
}

console.log(obj.getName());// new object
// this -> 誰調用 this 他就會指向誰
```
注意(this):
每個類定義只有一個函數被分配,而不是每個實例會創建一個函數
基類方法可以用super調用(優於箭頭函數)

延伸閱讀：[箭头函数](https://rexdainiel.gitbooks.io/typescript/content/docs/arrow-functions.html)

## this 類型
this 類型會動態指向當前類型
以回傳this值做範例 

範例一
```
class Box {
  content: string = ""

  set(value: string){
    this.content = value
    return this
  }
}

class ClearableBox extends Box {
  clear() {
    this.content = ""
    return this
  }
}

const a = new ClearableBox()
const b = a.set("hello")
console.log(b)// ClearableBox { content: 'hello' }
```

範例二(this作為參數的註釋)
```
class Box{
  content: string = "";

  sameAs(other: this): boolean{
    return this.content === other.content;
  }
}

class DerivedBox extends Box{
  derivedContent: string = "?";
}

const base = new Box();
const derive = new DerivedBox();
//derive.sameAs(base);// 報錯 Argument of type 'Box' is not assignable to parameter of type 'DerivedBox'.

console.log(base.sameAs(derive));// true
```

## 基於類型守衛的this
```
this is Type
```
`this is ` 是固定寫法,`Type`是某種類型,當類型縮小後,類型會被指定到Type類型

範例一
```
class FileSystemObject {
  isFile(): this is FileRep {
    return this instanceof FileRep;
  }
  isDirectory(): this is Directory {
    return this instanceof Directory;
  }
  isNetworked(): this is Networked {
    return this.networked;
  }

  constructor (public path: string, public networked: boolean) {}
}

class FileRep extends FileSystemObject{
  constructor(path: string, public content: string) {
    super(path, false);
  }
}

class Directory extends FileSystemObject{
  children: FileSystemObject[];
  constructor(){
    super('', false);
    this.children = [];
  }
}

interface Networked{
  host: string
}

const fso: FileSystemObject = new FileRep('foo/bar.txt', "foo");
if (fso.isFile()) {
  // const fso: FileRep
  fso.content
}else if(fso.isDirectory()){
  // const fso: Directory
  fso.children
}else if(fso.isNetworked()){
  // const fso: Networked
  fso.host
}
```

範例二(檢測值是否存在)
```
class Box<T> {
  value?: T;

  hasValue(): boolean {
    return this.value !== undefined;
  }
}

const box = new Box<number>();
box.value = 42;

if (box.hasValue()) {
  console.log(box.value); // 42
}
```

## 參數屬性
TS提供特殊語法,可以將構造函數的參數變成具有相同名稱和值的屬性
把這些屬性稱為 參數屬性
->簡單來說就是構造函數中的參數直接變成類屬性(還有相同的可見性)
```
class Params {
  constructor(public name: string, protected age: number) {}
}

const p = new Params('John', 30);
console.log(p.name);// John
//console.log(p.age);// 報錯(protected)
```

## 類表達式
使用表達式的方式來創建類(泛型)
```
const someClass = class<Type> {
  content: Type;

  constructor(content: Type) {
    this.content = content;
  }
};

const m = new someClass<string>("Hello World");
console.log(m.content); // Hello World
```

## 抽象類和成員
抽象類和成員不能直接實例化
目的：作為子類的基類,實現抽象成員

```
abstract class Base{
  abstract getName(): string

  printName(){
    console.log(this.getName())
  }
}

class Derive extends Base{
  getName(): string {
    return "world"
  }
}

const b = new Derive
console.log(b.getName())// world
b.printName()// world

// 抽象構造簽名

function greet(ctor: typeof Base) {
  const instance = new ctor(); // 會報錯：Cannot create an instance of an abstract class
}

function greet2(ctor: new() => Base){// 聲明了 Base 類型的結構化簽名
  const instance = new ctor()
  instance.printName() // 可以調用
}

// 測試
greet2(Base)// 報錯：Argument of type 'typeof Base' is not assignable to parameter of type 'new () => Base'.
greet2(Derive)
```

## 類之間的關係
相同的類,可以替代使用(賦值)
```
class Point1{
  x = 0
  y = 0
}

class Point2{
  x = 0
  y = 0
}

const p: Point1 = new Point2()
// 相同的類可以互相替代,內容和類型都要是一致的

// TS 會不會自動去識別基類和子類的關係->會
class Person {
  name: string = ""
  age: number = 100
}
class Employee {
  name: string = ""
  age: number = 100

  salary: number = 1000
}

// 自動辨識的子類,可以賦值給父類
const p2: Person = new Employee()
// 自動辨識的父類,不可以賦值給子類
//const p3:Employee  = new Person();// 報錯：Property 'salary' is missing in type 'Person' but required in type 'Employee'.

// 空類的情況(作為傳入參數)->相當於空函數->傳入任何參數都沒問題
class Empty{

}

function fn(x: Empty){

}

// 傳入什麼都沒問題
fn(window)
fn({})
fn(fn)
fn(100)
fn(100)
```


