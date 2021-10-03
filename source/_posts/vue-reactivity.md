---
title: vue3如何进行深度监听
---

### 先看表现 ref

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
/*
		{
			__v_isRef: true,
			_rawValue: 0,
			_shallow: false,
			_value: 0,
			value: 0
		}
	**/
console.log(fiirre2);
/*
		{
			__v_isRef: true,
			_rawValue: a: 1, b: 2, c: {…},
			_shallow: false,
			_value: Proxy {a: 1, b: 2, c: {…}},
			value: Proxy {a: 1, b: 2, c: {…}}
		}
	**/
```

v_isRef 这个用来判断是否是 ref 创建的

```javascript
export function isRef(r: any): r is Ref {
  return Boolean(r && r.__v_isRef === true)
}
```
