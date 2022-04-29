## 變量上的類型註釋
let variable_name: type = value;

如果ts可以推斷出變數類型,就會直接推斷

## 對象類型
```
function printCoord(pt:{x:number, y:number}): void {
    console.log(`x: ${pt.x}, y: ${pt.y}`);
}

printCoord({x:1, y:2});
// x: 1, y: 2
function printName(obj: {first: string;last?: string}): void {
    console.log(`${obj.first} ${obj.last}`);
    console.log(obj.last?.toUpperCase());// 問號把undifined過濾掉
}

printName({first: "John", last: "Doe"});
// John Doe
// DOE
printName({first: "John"});
// John undefined
// undefined

```

## 聯合類型
```
// 聯合類型 (union type)
// 可以把多個類型聯合成一個類型
let id : number | string;
id =100;
console.log(id);//100
id ="100";
console.log(id);//100

// 用判斷來讓類型變得更加清楚
function printId(id: number | string) {
  if (typeof id === "number") {
    console.log(id);
  } else {
    console.log(id);
  }
}

printId(100);//100
printId("100");//100

// 範例 2
function welcomePeople(x: string[]|string){
    if (Array.isArray(x)){
        console.log(x.join(","));
    } else{ 
        console.log(x);
    }
}

welcomePeople(["a","b","c"]);// a,b,c
welcomePeople("a");// a

// 範例 3
function getFirstThree(x: number[]|string): number[]|string{
    if (Array.isArray(x)){
        return x.slice(0,3);
    } else{ 
        return x.substr(0,3);
    }
}

console.log(getFirstThree("abcsdfa"));// abc
console.log(getFirstThree([1,2,3,4,5]));// [1,2,3]
```

## 類型別名
```
type Point= {
  x: number,
  y: number
}

function printCoordinates(point: Point) {
  console.log(`(${point.x}, ${point.y})`);
}

printCoordinates({x: 1, y: 2});// (1, 2)

type Id = string | number;
function printId(id : Id) {
  console.log(id);
}

printId(1); // 1
printId("hello"); // hello

// 自訂類型作為返回值
type UserInputSanitizedString = string;
function sanitizeInput(str: string): UserInputSanitizedString {
  return str.replace(/[^a-zA-Z0-9]/g, "");
}
let sanitizedString = sanitizeInput("Hello world");
console.log(sanitizedString);// Helloworld
```

## 接口
typescript 是結構類型的類型系統->只關心類型的結構和功能
```
// 接口
interface Point{
  x: number;
  y: number;
}

function printCoord(pt: Point){
  console.log(`x: ${pt.x}, y: ${pt.y}`);
}

printCoord({x: 1, y: 2});// x: 1, y: 2
```

### 類型別名(type)和接口的差異
- 擴展
- 向現有類型添加字段

#### 擴展
##### 擴展接口
```
interface Animal {
  name: string;
}

interface Bear extends Animal {
  hair: string;
}

const bear: Bear = {
  name: 'Yogi',
  hair: 'Brown',
};

console.log(bear);// { name: 'Yogi', hair: 'Brown' }
```

##### 交叉擴展(類型別名)
```
type Animal = {
  name: string;
  age: number;
};

type Bear = Animal & {
  hair: string;
};

const bear: Bear = {
  name: "Yogi",
  age: 500,
  hair: "Brown",
};

console.log(bear); // { name: 'Yogi', age: 500, hair: 'Brown' }
```

#### 向現有類型添加字段
##### 向接口添加字段
```
interface MyWindow {
  title: string
}
// 重複定義 myWindow 來添加 count 屬性
interface MyWindow {
  count: number
}

const window_1:MyWindow = {
  title: "Hello World",
  count: 1
}

console.log(window_1); // { title: 'Hello World', count: 1 }
```
##### 向類型添加字段
->類型創建後就無法更改或擴展->但可以透過繼承(&)來擴展

#### 總結
一般而言,使用接口定義的類型都能用type來定義
使用 extend 擴展接口
用 & 擴展類型

接口還能透過定義同名的接口來擴展字段