## 對象類型
在 js 中,分組和傳遞數據的基本方式,是透過對象來完成的,在ts中,透過對象來表達這些對象

對象基本知識：
- 匿名對象
- 接口命名(interface)
- 類型別名(type)

```
// 定義函數中的對象

// 使用匿名對象方式定義
function greet1( person:{name: string, age: number}){
  return "Hello, " + person.name;
}

// 在接口中定義
interface Person1 {
  name: string;
  age: number;
}

function greet2(person: Person1) {
  return "Hello, " + person.name;
}

// 類型別名方法定義
type Person2 = {
  name: string;
  age: number;
}

function greet3(person: Person2) {
  return "Hello, " + person.name;
}
```

## 屬性修改器(modifier, 修飾器)

### 對象類型的可選屬性
```
type Shape = {}

interface PaintOptions{
  shape: Shape;
  // 可選參數
  xPos?: number;
  yPos?: number;
}

// 用解構方式設定默認值
function paintShape({shape, xPos = 0, yPos = 0}: PaintOptions): void{
  console.log(`Paint ${shape} at (${xPos}, ${yPos})`);
}

const shape: Shape = {}
paintShape({shape})// Paint [object Object] at (0, 0)
paintShape({shape, xPos: 1})// Paint [object Object] at (1, 0)
paintShape({shape, yPos: 2})// Paint [object Object] at (0, 2)
paintShape({shape, xPos: 1, yPos: 2})// Paint [object Object] at (1, 2)
```

### 對象類型的只讀屬性
```
interface SomeType {
  readonly prop: string;
}

function donSomething(obj: SomeType){
  console.log(obj.prop);
  //obj.prop = 'something';// 報錯：Property 'prop' is readonly
}

// 嵌套的只讀屬性

interface Home{
  readonly resident: {
    name: string;
    age: number;
  }
}

function visitForBirthday(home: Home){
  console.log(home.resident.name);
  home.resident = {name:"", age:0};// 報錯：Property 'resident' is readonly"};
  home.resident.name = 'something';// 不會報錯

  // 結論：只有嵌套的當前屬性才有只讀屬性，如果嵌套的是非當前屬性(子屬性)，那麼不會有只讀屬性
  // 內存地址沒變
  // 可以分層定義權限
}

// 使用屬性別名來改變
interface Person{
  name: string
  age: number
}

interface ReadonlyPerson{
  readonly name: string
  readonly age: number
}

let writablePerson: Person = {name: 'Felix', age: 18};
let readonlyPerson: ReadonlyPerson = {name: 'Fenan', age: 20};
let readonlyPerson2: ReadonlyPerson = writablePerson;//只讀的屬性,用可寫的屬性賦值

console.log(readonlyPerson2.age)// 18
writablePerson.age++;
console.log(readonlyPerson2.age)// 19
```

### 對象類型使用索引簽名
可以任意地為一個對象定義屬性
```
interface StringArray {
  [index: number]: string;// [key: keytype]: valuetype
}

const myArray: StringArray = ["Bob", "Fred"];
console.log(myArray[0]);// Bob

interface TestArray {
  [props: string]: number;
}

let testArray: TestArray = { "0": 1, "1": 2, x:2, y:3 };
console.log(testArray["0"]);// 1

//

interface Animal {
  name: string;
}

// 繼承
interface Dog extends Animal {
  breed: string;
}

interface NotOkay {
  [index: number]: number | string;// 使用聯合類型,所以下面兩個參數都可以,如果使用單一類型,則不符合的會報錯
  length: number;
  name: string;
}

let notOkay: NotOkay = { name: "Bob", length: 10 };

// 只讀的索引簽名
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let myArray2: ReadonlyStringArray = ["Alice", "Bob"];
//myArray2[2] = "Mallory";// 報錯,因為索引簽名為readonly

```
延伸閱讀：https://dmitripavlutin.com/typescript-index-signatures/

### 擴展類型
```
interface BasicAddress {
  name?: string;
  street?: string;

}

interface AddressWithUnit extends BasicAddress{
  unit: string
}

let awu: AddressWithUnit = {
  name: 'John Smith',
  street: '123 Main St',
  unit: 'Apt. B'
}

console.log(awu);// { name: 'John Smith', street: '123 Main St', unit: 'Apt. B' }

// 多重擴展

interface Circle{
  radius: number
}

interface Colorful{
  color: string
}

interface ColorfulCircle extends Circle, Colorful{

}

let cc: ColorfulCircle = {
  radius: 1,
  color: 'red'
}

console.log(cc);// { radius: 1, color: 'red' }
```

### 交叉類型
```
interface Circle{
  radius: number
}

interface Colorful{
  color: string
}

type ColorfulCircle = Circle & Colorful

// 定義變量
let cc: ColorfulCircle = {
  radius: 1,
  color: 'red'
}

console.log(cc);// { radius: 1, color: 'red' }

// 定義函數
function draw(circle: Circle & Colorful){
  console.log(circle.radius);
  console.log(circle.color);
}

draw({radius:10,color:'green'});
//10
//green
```

### 接口與交叉類型
interface 重複定義會擴展
type 不能重複定義

### 泛型對象
多種型別變數的對象有以下幾種聲稱方式：
1. 設定為 unknow然後用類型縮小去執行
2. 定義多個對象後使用重載簽名去做到
3. 使用泛型來定義

```
interface Box<Type> {
  value: Type;
}

let box: Box<number> = { value: 0 };

interface Apple {}

let apple: Apple = {};

let appleBox: Box<Apple> = { value: apple };

// 使用類型別名來定義泛型對象

type Box2<Type> = { value: Type };

// 定義通用的輔助類型
//通用對象類型和其所包含的元素無關
// 如此定義可以在不同數據之間重複使用

type OrNull<Type> = Type | null;
type OneOrMany<Type> = Type | Type[];
type OneOrManyOrNull<Type> = OrNull<OneOrMany<Type>>;
type OneOrManyOrNullString = OneOrManyOrNull<string>;
```
