## 模塊
目前流行的使用方式
ES module, CommonJS
在 import 和 export 中的都是模塊
模塊中的參數非全局(除非明確地被導出,否則在模塊中才能使用)
使用其他模塊中變數時,需要將其導入

TS 中的模塊：
在 TS 中編寫基於模塊的程式碼時, 有三個主要方面需要考慮：
- 語法：想用什麼語法來導入和導出東西
- 模塊解析：模塊名稱(或路徑)和磁盤上的文件之間,是什麼關係
- 模塊輸出目標：編譯出來的 JS 模塊應該是什麼樣子的

### 範例一
`app.ts`
```
import helloWorld from "./module";
helloWorld();// Hello, World
```
`module.ts`
```
export default function helloWorld(){
    console.log("Hello, World")
}
```
### 範例二
`module.ts`
```
export var pi = 3.14
export let squreTwo = 1.41
export const phi = 1.61
export default class RandomNumberGenerator {}
export function absolute(num: number){
    if (num<0) return num*=(-1)
    return num
}
```
`app.ts`
```
import { pi, squreTwo, phi, absolute } from "./module";
import ReGen from "./module";// 帶 default 的不用花括號,而且可以直接用別名的方式引用
// 額外導入語法
import { pi as p } from "./module";

// 全部引用
import * as math from "./module"

console.log(p); //3.14
console.log(absolute(pi * -1)); // 3.14

const rnGen = new ReGen
console.log(rnGen)// RandomNumberGenerator {}

const rnGen2 = new math.default()
console.log(rnGen2)// RandomNumberGenerator {}
```

## TS 特定的 ES 模塊語法

### 範例一
`module.ts`
```
type Cat = {
  breed: string;
  yearOfBirth: number;
};

type Dog = {
  breeds: string[];
  yearOfBirth: number;
};

const createCatName = () => "fluffy"
export { Cat, Dog ,createCatName};
```
`app.ts`
```
// import type { Cat, Dog} from "./module"
// import { createCatName } from "./module"
// 以上兩者等同於以下
import  { createCatName,type Cat,type Dog } from "./module";

// 以下兩個均會報錯 ：'createCatName' cannot be used as a value because it was imported using 'import type'
// import type{ createCatName } from "./module"//
// import type { Cat, Dog, createCatName } from "./module";

type Animals = Cat | Dog 

console.log(createCatName())// fluffy
```

### 範例二(ES 模塊與 CommonJS行為)
這種語法保證在輸出時和 CommonJS 語法有一對一的匹配
```
import fs = require("fs");
const code = fs.readFileSync("hello.ts","utf-8");
```

## CommonJS 語法
CommonJS是npm上大多數模塊的交付格式
就平常使用場景來說,使用之前所述的ES語法已經足夠
但如果要進一步,使用CommonJS對做代碼調適很有幫助

導出：`module`,`module.exports`
導入：`require`

下載
```
yarn add @types/node
```

### 範例

`module.ts`
```
function absolute(num: number) {
  if (num > 0) return (num *= -1);
  return num;
}

function absolute2(num: number) {
  if (num > 0) return (num *= -1);
  return num;
}
module.exports = {
    pi: 3.14,
    squareTwo: 1.41,
    absolute2
};

//module.absolute = absolute
```
`app.ts`
```
const math = require("./module");
console.log(math.pi); //3.14
```