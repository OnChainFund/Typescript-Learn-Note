## 類(class)
類成員
- 類屬性
- readonly
- 構造器
- 方法
- Getters/Setters
- 索引簽名

### 類屬性
```
class Point{
  x
  y

  constructor(x: number, y: number){
    this.x =x
    this.y =y 
  }
}

const pt = new Point(1,2)
console.log(pt)

// 如果無法控制初始化的過程,可以加!
// 相當於使用斷言操作符 !
class OkGreeter{
  name!: string
}
```

### readonly
防止在構造函數外賦值
使用場景：生日?
```
class Greeter {
  readonly name: string = 'World';

  constructor(name?: string) {
    if (name !== undefined) {
      this.name = name;
    }
  }

  err(){
    //this.name = "err";
  }
}

const g = new Greeter("name");
console.log(g.name);//name
//g.name = "new name";//error
```

### 構造函數 
```
// 衍生的類型構造函數必須使用super調用
class Base{
  k = 4

}

class Derive extends Base{
  constructor(){
    super()
    console.log(this.k)
  }
}

const d = new Derive()
// 4
```

### 類型方法
```
class Point {
  x=10
  y=20

  scale(factor: number) {
    this.x *= factor
    this.y *= factor
  }
}

const p = new Point()
console.log(p.x)//10
p.scale(2)
console.log(p.x)//20

// 獲取 class 外的變數

let x: number = 10

class C {
  x: string = 'hello'

  m(){
    this.x = 'world'// class 中的 x
    x = 20 // 外面的 x
  }
}
```

### 訪問器(getters/setters)
如果只存在 get 但沒有 set, 則該屬性是只讀的
如果沒有指定 setter 參數類型,它將從 getter 的返回類型中推斷出來
getter和setter必須有相同的成員可見性

```
class C {
  _length = 0;
  // 設置訪問器(通常還會再加一些邏輯)
  // 設置 getter
  get length() {
    return this._length;
  }
  // 設置 setter
  set length(value) {
    this._length = value;
  }
}

let c = new C();
// 調用 setter
c.length = 10;

// 調用 getter
console.log(c.length); // 10

// 範例二

class Thing {
  _size = 0;

  get size() {
    return this._size;
  }

  set size(value: string | number | boolean) {
    let num = Number(value);

    if (!Number.isFinite(num)) { // 如果 num 不是數字
      this._size = 0
      return
    }

    this._size = num;
  }
}

let t: Thing = new Thing();
console.log(t.size); // 0
t.size = 900;
console.log(t.size); // 900
t.size = "asdf";
console.log(t.size); // 0
```

### 類成員的索引簽名
```
class MyClass {
  [s: string ]: boolean |((s: string) => boolean);

  x: boolean =true
  
  check(s: string){
    return this[s] as boolean
  }
}
```