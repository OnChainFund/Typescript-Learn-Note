## 在條件類型內推理
用infer聲明新變量型別->使我們不必考慮一些關於數據類型的問題
```
// 限定要有返回值就成立(回傳Return)
// 否則就是never
type GetReturnType<T> = T extends (...args: never[]) => infer Return
  ? Return
  : never;

// type Num = number
type Num = GetReturnType<() => number>;
let num: Num = 100;

// type Str = string
type Str = GetReturnType<(x: string) => string>;
let str: Str = "hello";

// type Bool = boolean
type Bool = GetReturnType<(a: boolean, b: boolean) => boolean[]>;
let bools: Bool = [true, false];

// type Never = never
type Never = GetReturnType<string>;
let nev: Never = "error" as never;

// 當有一個具有多個調用簽名的類型(ex.使用重載函數)
// 類型會由最後一個簽名進行推斷
// 不會根據參數的列表,來重新執行推斷和解析
// 範例如下

function stringOrNumber(x: string ): number
function stringOrNumber(x: number) : string
function stringOrNumber(x: string | number) : string | number 
function stringOrNumber(x: string | number): string | number {
  return Math.random() > 0.5 ? x : x.toString();
}// 實現簽名 

// type T1 = string | number
type T1 = ReturnType<typeof stringOrNumber>;
//const t1: T1 = true;// 報錯：Cannot assign 'true' to 'string | number'
```

## 分布式條件類型
```
// 範例一
type ToArray<Type> = Type extends any ? Type[] : never;

// type StringArray : string[] | number[]
type StrArrOrNumArr = ToArray<string | number>;
let strArrOrNumArr: StrArrOrNumArr = ['a', 'b', 'c'];
//let strArrOrNumArr2: StrArrOrNumArr = ["a", "b", "c",1]：
// 報錯：Type '(string | number)[]' is not assignable to type 'string[]'.

// 範例二
type ToArray2<Type> = [Type] extends [any] ? Type[] : never;

// type StringArray :(string | number)[]
type StrArrOrNumArr2 = ToArray2<string | number>;
let strArrOrNumArr2: StrArrOrNumArr2 = ['a', 'b', 'c',1,2,3];
```