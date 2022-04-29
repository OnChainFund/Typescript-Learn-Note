## 類型斷言
聲明方式
- as
- <>
與類型註釋一樣,類型斷言由編譯器刪除,不影響程式運行時行為
所以沒有與類型斷言相關聯的運行時檢查
```
const myCanvas1 = document.getElementById('my_canvas') as HTMLCanvasElement;
const myCanvas2 = <HTMLCanvasElement>document.getElementById('my_canvas')  ;
// 以上兩種語法等價
```
ts 只允許用類型斷言來讓類型更具體
```
// 範例
//const x = 'hello' as number; // 會報錯,因為string不是number的子類型
// 解法：先把 x 定義成 any
const x = ('hello' as any) as number; // 可以解決
```
類型斷言在使用ts時,可以把未知的變量類型斷言為一個差不多的類型,而不是使用any(失去使用ts的意義)

## 文字類型
使用`let`來聲明字符串,可以改變
`const`不能改變
```
let testString_1 = 'Hello World';
testString_1 = 'Hello TypeScript';

const testString_2 = 'Hello World';
//testString_2 = 'Hello TypeScript';// 會報錯,因為const 不能重新賦值

// 字符串類型組合
let w: 'hello' = 'hello';//只能賦予一種值的字串類型
console.log(w);// hello
//w = "world";// 報錯,因為w只能賦予hello

//類型組合用法
function printText(s: string, alignment: 'left' | 'right' |'center') {
  // alignment 只能賦予left, right, center三種值
  console.log(s); // hello
  console.log(alignment); // left
}
printText('hello', 'left');
```

文字類型,數字,boolean可以任意組合
```
function handleRequest(url: string, method: "Get" | "Post"): void {
  console.log(`${method} request for ${url}`);
}

const req_1 = {
  url: "http://example.com",
  method: "GET",
};

const req_2 = {
  url: "http://example.com",
  method: "GET",
};
//handleRequest(req_1.url, req_1.method);//會報錯,因為req_1.method是字符串類型,範圍大過Get或Post
handleRequest(req_2.url, (req_2.method as "GET"));
```

## null 和 undifined 類型
```
let x: undefined = undefined;
let y: null = null;
//let z : number = undefined;// 報錯：不能把 undefined 轉型為 number

function doSomeThing(x: string | null): void {
  if (x == null) {
    console.log(" is null");
  } else{
    console.log('Hello', x.toUpperCase());
  }
}
doSomeThing(y);// is null
doSomeThing("Mike");// Hello MIKE

// 用驚嘆號來檢查 null和undefined
function doSomeThing2(x?: string | null): void {// ?表示參數可選
  console.log(x!.toUpperCase());// !表示不可為undefined
  // ! 必須確認不可能是undefined或null類型，否則報錯
  // 應避免過多使用
}

//doSomeThing2(null);// 報錯
doSomeThing2("Mike");// MIKE
```

## 枚舉
```
enum Direction{
  Up = 1,
  Down = 2,
  Left = 3,
  Right = 4,
}

console.log(Direction.Up);// 1
```

## 原語
### 不太常用
bigint: 非常大的整數
symbol: 用函數創建,全局唯一引用
```
const big1: bigint = 100n;
const big2: bigint = BigInt(100);

const firstName = Symbol('name');
const lastName = Symbol('name');

// if (firstName == lastName) {
//   console.log('Symbols are equal');
// } ->報錯,Symbol為唯一值,type不一樣
```

