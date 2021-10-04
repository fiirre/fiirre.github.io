---
title: promiseA+
---

```javascript
class Promise {
  // promise 必须要三个状态，pending, fulfilled, rejected
  static PENDING = "pending";
  static FULFILLED = "fulfilled";
  static REJECTED = "rejected";
  // 返回一个全新的Promise实例
  static deferred() {
    let defer = {};
    defer.promise = new Promise((resolve, reject) => {
      defer.resolve = resolve;
      defer.reject = reject;
    });
    return defer;
  }
  static resolvePromise(thenPromise, x, resolve, reject) {
    // 防止将自己传入自己的promise内部
    // promise和x引用同一个对象，用一个TypeError作为原因来拒绝promise
    if (x === thenPromise) return reject(new TypeError("循环引用"));
    // 如果 x 是函数或者对象
    if (x && (typeof x === "function" || typeof x === "object")) {
      // 添加一个标志，防止别调用两次
      let called = false;
      try {
        // 如果x是一个对象或函数
        // 让then成为x.then
        let then = x.then;
        if (typeof then === "function") {
          //  如果then是一个函数，this指向x。两个参数onResolve()，onRejected()
          then.call(
            x,
            (y) => {
              if (called) return;
              called = true;
              // 递归处理 y 直到resolve中传入的是一个普通值
              Promise.resolvePromise(thenPromise, y, resolve, reject);
            },
            (r) => {
              if (called) return;
              called = true;
              reject(r);
            }
          );
        } else {
          resolve(x);
        }
      } catch (e) {
        // 如果检索属性x.then导致抛出了一个异常e，用e作为原因拒绝promise
        if (called) return;
        called = true;
        reject(e);
      }
    } else {
      resolve(x);
    }
  }
  static setTimeoutResolvePromise(callback, data) {
    let { promise, resolve, reject } = data;
    //  在执行上下文栈中只包含平台代码之前onFulfilled或onRejected一定不能被调用
    setTimeout(() => {
      try {
        Promise.resolvePromise(promise, callback(this.data), resolve, reject);
      } catch (e) {
        reject(e);
      }
    });
  }
  static directFnConstructor(state) {
    return (value) => {
      if (this.state !== Promise.PENDING) return;
      this.state = state;
      this.data = value;
      this.state === Promise.FULFILLED
        ? this.onFulfilledCallbacks.map((fn) => fn())
        : this.onRejectedCallbacks.map((fn) => fn());
    };
  }
  static resolve(value) {
    return new Promise((resolve, reject) => {
      resolve(value);
    });
  }
  static reject(value) {
    return new Promise((resolve, reject) => {
      reject(value);
    });
  }
  static all(promiseArr) {
    let len = promiseArr.length;
    let i = 0;
    let count = 0;
    let arr = [];
    return new Promise((resolve, reject) => {
      while (i < len) {
        promiseArr[i++].then((value) => {
          arr[count] = value;
          count++;
          if (count === len) {
            resolve(arr);
          }
        }, reject);
      }
    });
  }
  static race(promiseArr) {
    let len = promiseArr.length;
    let i = 0;
    return new Promise((resolve, reject) => {
      while (i < len) {
        promiseArr[i++].then(resolve, reject);
      }
    });
  }
  constructor(executor) {
    // 初始状态 PENDING
    this.state = Promise.PENDING;
    // 存储 resolve  reject  传入的值
    this.data = null;
    // 存储 then 传入的onFulfilled [] 回调函数
    this.onFulfilledCallbacks = [];
    // 存储 then 传入的onRejected [] 回调函数
    this.onRejectedCallbacks = [];
    //  过多重复代码 directFnConstructor 返回函数
    // 改变this，防止内部获取不到 Promise实例的属性
    const resolve = Promise.directFnConstructor.call(this, Promise.FULFILLED);
    const reject = Promise.directFnConstructor.call(this, Promise.REJECTED);
    // 捕捉   executor 执行的时候错误捕捉
    try {
      /**
       * new Promise((resolve, reject) => {
       *    throw new Error("xxx")
       * 	resolve(1)
       * })
       */
      // 执行器函数是同步调用的
      executor(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }
  then(onFulfilled, onRejected) {
    // 这一步防止 传入非函数，进行校验，统一处理
    onFulfilled =
      typeof onFulfilled === "function" ? onFulfilled : (value) => value;
    onRejected =
      typeof onRejected === "function"
        ? onRejected
        : (reason) => {
            throw reason;
          };
    //  创建一个包含Promise对象的各种方法的对象
    let data = Promise.deferred();
    if (this.state !== Promise.PENDING) {
      // 当前状态不是PENDING ，判断当前用哪个回调
      let fn = this.state === Promise.REJECTED ? onRejected : onFulfilled;
      Promise.setTimeoutResolvePromise.call(this, fn, data);
    } else if (this.state === Promise.PENDING) {
      // 将 then 中传入的onFulfilled onRejected 放入 相应的队列
      this.onFulfilledCallbacks.push((_) => {
        Promise.setTimeoutResolvePromise.call(this, onFulfilled, data);
      });
      this.onRejectedCallbacks.push((_) => {
        Promise.setTimeoutResolvePromise.call(this, onRejected, data);
      });
    }
    // 返回一个全新的对象 ，后面链式调用
    return data.promise;
  }
  catch(onRejected) {
    return this.then(null, onRejected);
  }
  finally(fn) {
    return this.then(fn, fn);
  }
}
module.exports = Promise;
```
