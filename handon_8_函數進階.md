## 普通函數可選參數
- `?`->參數為可選(可加可不加)
- 加上`=`設定預設值(若不傳值時使用)
以上兩者不能同時使用

## 回調函數的可選參數
為回調寫一個類型函數時,永遠不要寫一個可選參數
除非打算在不傳遞該參數的情況下調用該參數
因為類型檢查會直接默認參數有可能不傳,所以回調函數用了參數反而會報錯(因為類型)

例子：(也沒有講解到原理)
```
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}

myForEach([1, 2, 3], (item) => {
  console.log(item);
});
/*
1
2
3
*/
myForEach([1, 2, 3], (item, index) => {
  console.log(item,index);
});
/*
1 0
2 1
3 2
*/
```

## 函數重載
在 ts 中,可以透過編寫重載簽名,來指定一個可以以不同方式調用的函數(要寫兩個或更多的函數簽名,然後是函數主體)
```
function makeDate(timestamp: number): Date;// 重載簽名：時間
function makeDate(m: number, d: number, y:number):Date;// 重載簽名：日期(月, 日, 年)
function makeDate(mOrTimestamp: number, d?:number, y?:number): Date {
    if(d !== undefined && y !== undefined) {
        return new Date(y, mOrTimestamp, d);
    }else{
        return new Date(mOrTimestamp)
    }
}// 實現簽名,只是為了兼容重載簽名

console.log(makeDate(1234));// 第一種寫法
// 1970-01-01T00:00:01.234Z
console.log(makeDate(1, 2, 2018));// 第二種寫法
// 2018-02-01T16:00:00.000Z

```

重載簽名和實現簽名問題：
- 參數不正確
  - 在實現時,不會檢查實現簽名的參數
  - (只會檢查有無符合重載簽名的參數)
- 參數類型不正確
  - 實現函數必須兼容重載函數
  - ex.如果兩個重載函數的唯一參數型別不同,實現函數參數必須使用聯合類型
- 返回類型不正確
  - 實現函數必須兼容重載函數
  - ex.如果兩個重載函數的唯一返回值型別不同,實現函數返回值必須使用聯合類型

### 如何編寫好的重載函數
在可能的情況下,盡量使用聯合類型,而非重載函數

### 函數內的 this 聲明
ts 允許給函數傳入 this 並且使用 this 類型
```
interface User{
    admin: boolean
}

interface DB{
    filterUsers(filter:(this: User) => boolean): User[]
}

const db:DB = {
    filterUsers: (filter: (this: User)=>boolean)=>{
        let user1: User = {
            admin: true
        }
        let user2:User= {
            admin:false 
        }
        return [user1,user2]
    }
}

const admins = db.filterUsers(function(this: User){ 
    // 這個 function 使用this(不能是箭頭函數)
    // 箭頭函數不能包含 this 參數
    return this.admin
})
```

## 需要了解的其他類型
void: 沒有返回值函數的返回值(推斷出來的),跟js 沒有返回值函數的返回值(undifined)不同
object: 只任何非基元的值,基元(string,number,bigint,boolean,symbol,null,undifined) 
> 注意:object 不同於 {},也不同於 Object->屬性

unkown 類型代表任何值,與any相似但更安全,因為對未知unkown做任何事都是不合法的
never 類型表示永遠不會被觀察到的值(ex:函數錯誤,或不可能發生的事件)
Function: 全局的 Funciton 類型描述了諸如bind,call,apply和其他存在於js中所有函數值的屬性,它還有一個特殊屬性,即function類型總是可以被調用,這些調用會返回any

## 參數展開運算符--形參展開(可以讓函數接收無限數量的函數)(英文：rest parameter)
```
function multiply(n: number, ...m: number[]) {
  return m.map((x) => n * x);
}

console.log(multiply(10, 1, 2, 3, 4, 5));
// [ 10, 20, 30, 40, 50 ]
```

## 參數展開運算符--實參展開
一個數組,展開到一個調用函數中,當成是一個實參
```
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

arr1.push(...arr2);
console.log(arr1); // [ 1, 2, 3, 4, 5, 6 ]

// ts 不假定數組不可變->所以可能產生一些問題
// 以下為範例
const args = [8, 5];
// 用反正切(atan)來計算角來
const angle1 = Math.atan2(8, 9); //成功
//const angle2 = Math.atan2(...args);//報錯->A spread argument must either have a tuple type or be passed to a rest parameter.

// 解法：透過 as const 把數組的值換成常量
const args2 = [8, 5] as const;
const angle2 = Math.atan2(...args2); //成功
console.log(angle2);// 0.7266423406817256
```

## 參數解構
用參數重構的方法,將對象實參,解壓到函數主體的一個,或多個局部變量中
```
type ABC = {a: number, b: number, c: number}

function sum({a,b,c}:ABC):void{
  console.log(a+b+c);
}

sum({a: 1, b: 2, c: 3});// 6
```

## 返回 void 類型函數
(不返回任何東西)
意義：
一個具有 void 返回類型的"上下文函數類型"( type vf=()=> void)在實現時,可以返回任何其他值，但它會被忽略
當一個"字面值函數"定義有一個 void 的返回類型時, 該函數必須不返回任何東西
```
// 上下文函數類型
type voidFunction = () => void;

const f1: voidFunction = () => {
  return true;
};

const f2: voidFunction = () => true;

const f3: voidFunction = function () {
  return true;
};
const v1 = f1();
console.log(typeof v1);// boolean

console.log(f1()); // true
console.log(f2()); // true
console.log(f3()); // true
// 以上三種實現方式都是同樣效果

// 字面值函數
// 以下兩種定義方式均會報錯
// 報錯：Type 'boolean' is not assignable to type 'void'.

/*
function f4(): void {
  return true
}

function f5 = function(): void{
  return true
}

*/
```