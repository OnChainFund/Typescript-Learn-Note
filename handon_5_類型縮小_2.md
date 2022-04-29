## 受歧視的 union(聯合類型)
```
// interface Shape {
//   kind: "circle" | "square";
//   radius?: number;
//   sideLength?: number;
// }
// ->這樣定義因為 radius和 sideLength都是選填,所以會報錯->因為可能沒有傳進來
// 以下為解法(用聯合類型來解決)
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

type Shape = Circle | Square;


function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
  }
}
```

## Never 類型與窮盡類型檢查
在縮小類型時,當已經排除了所有可能性,ts將會使用never類型,來代表一個不存在的狀態
never可以分配給每個類型,但沒有類型可以分配給never(除了never本身以外)
->可以用switch 配上 never做窮盡檢查
```
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default: //理論上應該為never類型
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```