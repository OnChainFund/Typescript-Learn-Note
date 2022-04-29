## 類型操縱

### 從類型中創建類型
從現有類型中創建新類型的方法
- 泛型類型
- keyof 類型操作符
- typeof 類型操作符
- 索引訪問類型
- 條件類型
- 映射類型
- 模板字面量類型

### 泛型
軟體工程中有很大一部份在建立組件
組建必須要有的特性：
- 定義一致的API
- 重複使用

```
function identity<Type>(arg: Type): Type {
  return arg;
}

// 泛型函數
let myIdentity: <Type>(arg: Type) => Type = identity;

// 對象自變量
let myIdentity2: {<Type>(arg: Type): Type} = identity;

// 泛型接口
interface GenericIdentityFn {
  <Type>(arg: Type): Type;
}

let myIdentity3: GenericIdentityFn = identity;

// 更嚴謹的泛型接口(必須要在聲明時候明確標明類型)
interface GenericIdentityFn2<Type> {
  (arg: Type): Type;
}

let myIdentity4: GenericIdentityFn2<number> = identity;
```

### 泛型類
```
class GenericNumber<NumType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

let myGenericNumber2 = new GenericNumber<string>();
myGenericNumber2.zeroValue = 'asd';
myGenericNumber2.add = function(x, y) { return x + y; };
```

### 泛型約束
```
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

const output = loggingIdentity({ length: 10, value: 3 });
// 10

const output2 = loggingIdentity("asdf");
// 4
```

### 在泛型約束中使用類類型
```
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key];
}
// extends keyof -> Type 和 Key 是有關連的

let x = { a: 1, b: 2, c: 3, d: 4 };

console.log(getProperty(x, "a")); // okay
// 1
//getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

### 在泛型中使用類類型
```
// 工廠函數：用於創建類的實例
function create<Type>(c:{new():Type;}):Type {
  // 創建類的實例
  // 返回類的實例 
    return new c();
}

// 範例2
class BeeKeeper {
  hasMask: boolean = true;
}

class ZooKeeper {
  nametag: string = 'Zookeeper';
}

class Animal {
  numLegs: number = 4;
}

class Bee extends Animal{
  keeper: BeeKeeper = new BeeKeeper();
}

class Lion extends Animal{
  keeper: ZooKeeper = new ZooKeeper();
}

// 在泛型中使用類類型
function createInstance<A extends Animal>(c: new() => A): A {
  return new c();
}

console.log(createInstance(Lion).keeper.nametag); 
// Zookeeper
console.log(createInstance(Bee).keeper.hasMask);
// true
//createInstance(BeeKeeper);// 報錯  Property 'numLegs' is missing in type 'BeeKeeper' but required in type 'Animal'.
```

### keyof 類型操作符
```
type Point = { x: number; y: number };

type P = keyof Point; //P : 'x' | 'y'

const p1: P = "x";
const p2: P = "y";
//const p3: P = 'z';//報錯：

// 使用索引簽名來定義
type Arrayish = {
  [index: number]: number;
};
type A = keyof Arrayish; //A : number
const a1: A = 0;
//const a2: A = "1"; //報錯：Type 'string' is not assignable to type 'number'

// 範例二
type Mapish = {
  [index: string]: boolean;
};

type M = keyof Mapish; //M : string 或是 number(自動轉換成string)

const m1: M = "a";
const m2: M = 1;
const m3: M = "1";//和上移行相同

//const m4: M = true; //報錯：Type 'boolean' is not assignable to type 'number'
```

### typeof 類型操作符
在類型上下文中使用它來引用變量
如果只使用基本類型的話,typeof沒有什麼用處(使用基本類型就好)
但如果組合其他類型操作,可以使用typeof來方便地表達很多模式
```
console.log(typeof 'Hello World');//string
// 原生 js 的 typeof

// ts 的 typeof 可以返回類型
let s = 'Hello World';
let n: typeof s
n = "Hello World2";

// 預定義的型別

// Return type(返回函數返回值的類型)
type Predicate = (value: unknown) => boolean;
type K =ReturnType<Predicate>

function f(){
  return {
    x:10,
    y:20
  }
}

type P = ReturnType<typeof f>
const p: P = {x:10,y:20}
//const p2: P = {x:10,y:20,z:30}// 報錯：不符合類型

// 可以用typeof 去標示變量,或變量的屬性,但不能用來標示函數調用的結果
// 範例如下
function msgbox(){

}

let shouldContinue: typeof msgbox;//shouldContinue是void類型
```

### 索引訪問類型
```
interface Person {
  name: string;
  age: number;
  alive: boolean;
}

type N = Person["name"];
const n: N = "";

// 複合類型

// |

// 範例一
type BasicPerson = Person["name" | "age"];
const basicPerson1: BasicPerson = "";
const basicPerson2: BasicPerson = 100;

// 範例二(預定義類型)
type NameOrAge = "name" | "age";
type BasicPerson2 = Person[NameOrAge];
const basicPerson21: BasicPerson2 = "";
const basicPerson22: BasicPerson2 = 100;

// keyof
type I2 = Person[keyof Person];
const i2: I2 = "";
const i2_2: I2 = 100;
const i2_3: I2 = true;

// 獲取陣列的某個項目類型

const MyArray = [
  { name: "asdf", age: 100, alive: true },
  { name: "yyy", age: 100 },
  { name: "sas", age: 100, child: { name: "asdf", age: 100, } },
]

type SpecificPerson = typeof MyArray[number];
const specificPerson: SpecificPerson = { name: "", age: 0, alive: false };

type SpecificPersonName = typeof MyArray[number]["name"];
const specificPersonName: SpecificPersonName = "";
```

### 條件類型
```
interface Animal {
  live(): void;
}

interface Dog extends Animal {
  bark(): void;
}

// Example 1: number 
type Example1 = Dog extends Animal ? number : string;
const example1: Example1 = 1;
// Example 2: string 
type Example2 = RegExp extends Animal ? number : string;
const example2: Example2 = 'string';

// 條件類型的意義
// 跟泛型結合
// 可以避免使用函數重載(程式碼量大)
interface NameLabel{
  name: string;
}
interface IdLabel{
  id: number;
}
type NameOrId<T extends string | number> = T extends number ? IdLabel : NameLabel;

function createLabel<T extends string | number>(label: T): NameOrId<T> {
  throw ""
}

// type a = IdLabel
let a = createLabel(1);

// type b = NameLabel
let b = createLabel('1');

// type c = IdLabel | NameLabel
let c = createLabel(Math.random() > 0.5 ? 1 : '1');
```

### 條件類型約束
```
type MessageOf<T> = T extends {message: unknown } ? T['message'] : never;

interface Email{
  message: string;
}

interface Dog{
  bark(): void;
}

type EmailMessageContents  = MessageOf<Email>;
const emc: EmailMessageContents = 'hello';

type DogMessageContents = MessageOf<Dog>;
// const dmc: DogMessageContents = 'woof';// 報錯：Type 'string' is not assignable to type 'never'.
// 解法如下(添加類型斷言)
const dmc: DogMessageContents = "woof" as never;

// 範例3
// 查看T是否有any[]數組的約束
// 如果有,則返回數組中元素的類型
// 若無,則直接返回T類型
// 意即：只對數組類型做處理
type Flatten<T> = T extends any[] ? T[number] : T;

// type Str = string
type Str = Flatten<string>;

// type Num = number
type Num = Flatten<number>;
```