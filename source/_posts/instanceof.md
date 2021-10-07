---
title: instanceof
---

instanceof 运算符用于检测 构造函数的 prototype 属性 是否出现在某个 实例对象的原型链上。

实现

```javascript
function instanceOf(l, r) {
  let left = l.__proto__;
  let right = r.prototype;
  while (true) {
    if (left === null) {
      return false;
    }
    if (left === right) {
      return true;
    }
    left = left.__proto__;
  }
}
```

```javascript
if (!(mycar instanceof Car)) {
  // Do something, like mycar = new Car(mycar)
}
// 这和以下代码完全不同

if (!mycar instanceof Car)
// 这段代码永远会得到 false（!mycar 将在 instanceof 之前被处理，所以你总是在验证一个布尔值是否是 Car 的一个实例）。
```

参考:
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof
