## 類型縮小
窄化類型範圍
```
function padLeft(padding: number | string, input: string){ 
  if (typeof padding === "number") { // 類型保護->將類型限縮到比聲明更小的範圍
    return Array(padding + 1).join(",") + input;
  }
  if (typeof padding === "string") {
    return padding + input;
  }
}

console.log(padLeft(1, "Hello World")); // ,Hello World
console.log(padLeft("Hello ", "World")); // Hello World
```
範例 2
```

function printAll(strs: string | string[] | null){
  if (typeof strs === "object") {
    //string[]
    console.log(strs)
  } else if (typeof strs === "string") {
    //string
    console.log(strs)
  } else {
    //null
    console.log("null");
  }
}
printAll(null);//null
printAll("hello");//hello
printAll(["hello", "world"]);//[hello, world]
```

### 真值縮小
```
// 真值縮小：適合用於防範null, undifined

// 範例一
function printAll(strs: string | string[] | null) {
  //if (typeof strs === "object") {->會有問題,因為strs雖然是'object'但可能是null->加入真值縮小(如下)
  // for (const s of strs) { 
  //   console.log(s);
  // }
  if (strs && typeof strs === "object") {
    for (const s of strs) {
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    console.log("null");
  }
}

// 範例二
function multiplyAll(
  values: number[] | undefined,
  factor: number
){
  if (!values) {
    return values;
  }else{
    return values.map(n => n * factor);
  }
}

console.log(multiplyAll([1,2,3],2))// [ 2, 4, 6 ]
console.log(multiplyAll(undefined,2))// undefined
```

### 等值縮小
#### 嚴謹
=== ：全等
!== ：全不等
#### 寬鬆
== ：等於
!= ：不等於

#### 範例
```
function example(x: number | string, y: string | boolean) {
  if (x === y) { // 因為如果全等,那類型一定相等
    console.log("x and y are equal");
    console.log(x.toUpperCase());
    console.log(y.toUpperCase());
  }
}

example(10, true);
//
example(10, "10");
//
example("10", "10");
// x and y are equal
// 10
// 10
example("10", "1");
//
```

等值縮小對於檢查不是變量,而是字面量的值也是有效的
```
function printAll(strs: string | string[] | null) {
  if (strs !== null) {//
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    } else {
      console.log("null");
    }
  }
}
```

區別 null , undifined
```
interface Contianer {
  value: number|null|undefined;
}


function multipyValues(container: Contianer, factor: number):Contianer {
  // 過濾null和undefined
  if (container.value != null) {
    container.value *= factor;
  }
  return container;
}

console.log(multipyValues({value: null},8).value);// null
console.log(multipyValues({value: 10},8).value);// { value: 80 }
```

### in 操作符縮小
```
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void }; // ?->代表可選

function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    // 可能是 Fish | human
    return (animal as Fish).swim();
  }

    // 可能是 Bird | human
  return (animal as Bird).fly();
}
```

### instanceof 操作符縮小
檢查是否為某物件(對象)的實例
也是一種類型保護
```
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toISOString());
  }else{
    console.log(x.toUpperCase());
  }
}

logValue(new Date());// 2022-04-21T05:37:53.579Z
logValue("Hello world");// HELLO WORLD
```

### 分配縮小
```
// 定義 x 為聯合類型
// let x : string | number;(以下默認定義)
let x = Math.random() <0.5 ? 10 : 'World';

console.log(x);

x = 1;
console.log(x);

x = 'Hello';
console.log(x);

//x = true;// 會報錯->Type 'boolean' is not assignable to type 'string | number'.
```

### 控制流分析 
```
// 定義 x 為聯合類型
function example(){
  let x : number | string | boolean;// 起始狀態 x 為三種類型中的一種

  x = Math.random() < 0.5; //x 為 boolean
  console.log(x);

  if (Math.random() < 0.5) {
    x = Math.random(); //x 為 number
    console.log(x);
    return x
  }else{
    x = "Hello World"; //x 為 string
    console.log(x);
    return x
  }
}

// 檢測此時的 x 為什麼類型
let x = example()
x = 1; // x 可為 number
x = "hello"; // x 可為 string
//x = true; //報錯 x 不可為 boolean
```

### 使用類型謂詞
直接控制程式的類型變化
->定義函數返回值為類型謂詞
#### 範例一

定義
```
function isFish(pet: Fish | Bird): pet is Fish{
  // 返回值格式須為 parameterName is Type
  return (pet is Fish).swim !== undifined;
}
```

```
type Fish ={
  name: string,
  swim:()=>void,
}
type Bird ={
  name: string,
  fly:()=>void,
}

function isFish(pet: Fish | Bird): pet is Fish{
  // 返回值格式須為 parameterName is Type
  // 如果 return true,就是 Fish 類型
  // 如果 return false,就是 Bird 類型
  return (pet as Fish).swim !== undefined// 判斷是否有 swim 屬性
}

function getSmallPet(): Fish | Bird{
  let fish: Fish = {    
    name: '小鯨魚',
    swim: ()=>{}
  }
  let bird: Bird = {    
    name: '小鳥',
    fly: ()=>{}
  }

  return true? bird: fish
}

let pet = getSmallPet()

if (isFish(pet)){
  pet.swim()
}else{
  pet.fly()
}

// 使用類型守衛過濾type
const zoo: (Fish|Bird)[] = [getSmallPet(),getSmallPet(),getSmallPet(),getSmallPet()]
const underWaters1: Fish[] = zoo.filter(isFish);// 過濾數組
const underWater2: Fish[] = zoo.filter(isFish) as Fish[];

// 使用回調函數
// 類型謂詞嵌套重複使用
const underWater3: Fish[] = zoo.filter((pet): pet is Fish =>{
  if (pet.name === "from"){
    return false
  }
  return isFish(pet)
})
```