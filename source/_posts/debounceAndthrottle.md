---
title: 防抖和节流
---

防抖: 触发高频事件后 n 秒后函数只会执行一次，如果 n 秒内高频事件再次被触发，则 重新计算时间；

```javascript
function debounce(fn) {
  let timeout = null;
  return function () {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fn.apply(this, arguments);
    }, 500);
  };
}
```

防抖: 高频事件触发，但在 n 秒内只会执行一次，节流会稀释函数的执行频率。

```javascript
function throttle(fn) {
  let flag = true;
  return function () {
    if (!flag) {
      return;
    }
    flag = false;
    setTimeout(() => {
      fn.apply(this, arguments);
      flag = true;
    }, 500);
  };
}
```
