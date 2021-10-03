---
title: vue3如何进行深度监听
---

源码https://github.com/vuejs/vue-next/blob/master/packages/reactivity

ref 创建监听

```javascript
let fiirre1 = ref(0);
let fiirre2 = ref({
  a: 1,
  b: 2,
  c: {
    e: 3,
  },
});
console.log(fiirre1);
/**
		{
			__v_isRef: true,
			_rawValue: 0,
			_shallow: false,
			_value: 0,
			value: 0
		}
	*/
console.log(fiirre2);
/**
		{
			// v_isRef 这个用来判断是否是 ref 创建的
			__v_isRef: true,
			_rawValue: a: 1, b: 2, c: {…},
			_shallow: false,
			// 一个proxy对象  内部调用reactive生成
			_value: Proxy {a: 1, b: 2, c: {…}},
			value: Proxy {a: 1, b: 2, c: {…}}
		}
	*/
```

```javascript
function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}
export function isRef(r: any): r is Ref {
  return Boolean(r && r.__v_isRef === true)
}
class RefImpl<T> {
	...
	//  ref 内部 调用 toReactive
	this._value = _shallow ? value : toReactive(value)
	····
}
export const toReactive = <T extends unknown>(value: T): T =>
//  最后调用 reactive
  	isObject(value) ? reactive(value) : value
```

reactive 创建监听

```javascript
let fiirre = reactive({
  a: 1,
  b: 2,
  c: {
    e: 3,
  },
});
console.log(fiirre);
/**
 * Proxy {a: 1, b: 2, c: {…}}
 */
console.log(fiirre.c);
/**
 * 在获取fiirre.c 会触发proxy对象 get方法， 返回一个new proxy(c)
 * 在用到的时候代理->懒代理
 * Proxy {e: 3}
 */
```

懒代理

```javascript
// baseHandlers.ts
function createGetter(isReadonly = false, shallow = false) {
  	...
    if (isObject(res)) {
      // Convert returned value into a proxy as well. we do the isObject check
      // here to avoid invalid value warning. Also need to lazy access readonly
      // and reactive here to avoid circular dependency.
      return isReadonly ? readonly(res) : reactive(res)
    }

    return res
  }
}
```
