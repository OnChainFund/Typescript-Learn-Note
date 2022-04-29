## 泛型函數

泛型：兩個類型有相關聯
```
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0]
}

const s_1 = firstElement(["a","b","c"]);// s 是 string 類型
const s_2 = firstElement<string>(["a","b","c"]);// 加上指定類型(不用讓ts字型推斷)
const n_1 = firstElement([1,2,3]);// n 是 number 類型
const n_2 = firstElement<number>([1,2,3]);
const u_1 = firstElement([]); // u 是 undifined 類型
const u_2 = firstElement<undefined>([]); 
// 加類型定義：顯示類型

// 多個泛型
function map<Input,Output>(arr: Input[],func: (arg: Input)=> Output): Output[]{
  return arr.map(func);
}

const parsed = map(["1","2","3"], (n)=>parseInt(n))

console.log(parsed);// [ 1, 2, 3 ]
```

## 泛型參數的限制條件
用約束條件來限制函數可以接受的類型
```
// 創建一個 longest 函數來比較大小
// 使用泛型,但規定其必須擁有 length 且值為 number 的屬性
function longest<Type extends {length: number}>(num1: Type, num2: Type): Type{
  if (num1.length> num2.length){
    return num1
  }else if (num1.length == num2.length){
    return num1
  }else {
    return num2
  }
}

const longerArray = longest([1,2,3],[1,2])
console.log(longerArray)// [ 1, 2, 3 ]
const longerString = longest("hahaha","haha")
console.log(longerString)// hahaha
```

## 泛型函數使用受限值
使用通用約束條件的常見錯誤
```
function minimunLength<Type extends {length: number}>(
  obj: Type,
  minimun: number,
): Type{
  if (obj.length >= minimun){
    return obj
  }else{
    return { length: minimun }; 
    // 會報錯,雖然 Type 實現{length: number}
    // 但返回的類型不一定是Type
  }

}

const arr = minimunLength([1,2,3],6)
console.log(arr.slice(0))// 會有問題(此處回傳 undifined)
```

## 泛型指定類型參數
ts 通常可以推斷出預期的類型參數,但並非總是如此,所以有的時候需要指定
```
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}

//const arr0 = ([1, 2, 3], ["hello "]);// 因為沒有顯式規定類型,Type會自動取其中第一個作為類型->會報錯
const arr = combine<string | number>([1, 2, 3], ["hello "]);
console.log(arr);// [ 1, 2, 3, 'hello ' ]
// 但一般來說,不推薦手動規範類型,除非有業務需求
```

## 如何編寫優秀通用函數的準則
1. 盡量使用類型本身,而非對其進行約束
2. 盡可能少使用類型參數
3. 如果一個類型參數只出現在一個地方,請重新考慮是否要使用它

### 範例 

```
// 1
// 較好的實現(使用類型本身的 type)
function firstElement1<Type>(arr: Type[]){
  return arr[0]
}
// 較差的實現
function firstElement2<Type extends any[]>(arr: Type){
  return arr[0]
  // 這裡 ts 要使用類型約束來解析 arr[0]
  // 而不是調用期間,等待我們解析這個元素
  // 所以這個偏不好
}
const element_1 = firstElement1([1,2,3])
console.log(element_1)// 1

const element_2 = firstElement2([1,2,3])
console.log(element_2)// 1

// 2

// 較好的實現
function filter1<Type>(arr: Type[], func: (arg: Type) => boolean){
  return arr.filter(func)
}

// 較差的實現
function filter2<Type, Func extends (arg: Type) => boolean(
  arr:Type[],
  func: Func
){
  return arr.filter(func)
}
// 較差的原因
// 無緣無故增加一個手動的類型參數(Func),沒有功能,卻使得函數更難閱讀

// 3
類型參數是用來關聯多個值類型的,如果只使用一次,就沒有使用的必要｀

// 較好的實現(簡潔明瞭)
function greet1(s: string){
  console.log("hello"+s)
}

// 較差的實現
function greet2<Str extends string>(s: Str){
  console.log("hello"+s)

}
```