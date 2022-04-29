## 基礎

全局下載typescript編譯器
```
sudo npm install typescript -g
```
編譯檔案
```
tsc <filename>
```

## 優化編譯
1. 解決ts和js衝突問題(ex. 函數名衝突)
```
// 生成配置文件 -> tsconfig.json
tsc --init
```
2. 自動編譯
```
// 每次變化自動進行編譯
tsc --watch
```
3. 發出錯誤
```
tsc -noEmitOnError <filename>
```

## 顯式類型
有的時候ts會自動推斷,不需要主動聲明

## 降級編譯
可以修改tsconfig.json的target版本來改變編譯程式碼的目標版本

## 嚴格模式
tsconfig.json
```
"strict":true, // 所有最嚴格的標準的集合
"noImplicitAny": true, //隱式 any類型 -> 如果把所有類型退回any就失去typescript的意義
"strictNullChecks": true,
```

## 常用類型
### 基元類型
string
number
boolean
#### 特殊內置類型
String
Number
Boolean

### 數組
兩種聲明方式：
```
type[]
Array<type> // 泛型寫法
```
範例
```
let str: string = "Hello, World!";
let num: number = 123;
let bool: boolean = true;

let arr: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2, 3];
```
### any
當不希望某個特定值導致類型檢查錯誤時使用
```
let obj: any = {
    x: 0,
}

obj.foo();
obj();
obj.bar = 100;
// 都不會報錯,但執行的時候會有問題
```