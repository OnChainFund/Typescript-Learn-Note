## 函數
函數類型表達式：
```
fn: (a: string) => void
```

### 範例一(用函數作為傳入參數)
```
function greeter(fn: (a: string) => void) {// 把函數當成傳入的參數
  return fn('world');
}

function printToConsole(s: string){
  console.log(s);
}

greeter(printToConsole);// world
```

### 範例二(用類型別名代表函數當作傳入參數)
```
type Greeter= (a: string) => void
function greeter(fn: Greeter) {// 把函數當成傳入的參數
  return fn('world');
}

function printToConsole(s: string){
  console.log(s);
}

greeter(printToConsole);// world
```

## 調用簽名(把函數綁定屬性)
函數可以有屬性,但函數類型表達式的語法不能聲明屬性
->透過對象去定義
簽名：讓函數擁有屬性
```
type DescribableFunction = {
  description: string;// 賦予屬性 description
  (someArg:number): boolean;// 函數本身定義(參數:參數型別):回傳型別
}

function doSomething(fn: DescribableFunction){
  console.log(fn.description+' returned '+ fn(6));
}

// 傳入 doSomething 函數的值(fn)
function fn1(n: number): boolean{
  console.log(n);
  return n > 0;
}

// 把fn1 綁定 description 屬性
fn1.description = 'hello'; 

doSomething(fn1);
// Output
// 6
// hello returned true
```

## 構造簽名 -> 把 class 的 constructor 當成參數傳入
構造函數(new(會創建會創建一個新對象))

### 範例一
```
class Ctor{
  s: string
  constructor(s: string){
    this.s = s
  }
}
type SomeConstructor ={
  new (s:string):Ctor //構造簽名
}

function fn (ctor: SomeConstructor){
  return new ctor('Hello, World!')
}

const f = fn(Ctor);
console.log(f.s)// Hello, World!
```

### 範例二
```
interface CallOnConstructor {
  new (s: string): Date;
  (n?: number): number;
  // 以上意思為：
  // 如果要實例化的話要傳入string給Date作為初始值
  // 非實例化,傳入number
}

function fn(date: CallOnConstructor){
  let d = new date('2020-03-01');
  console.log(d);
  let n = date(100);
}
```