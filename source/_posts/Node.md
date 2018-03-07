title: Node Basic
author: Howard Wong
tags:
  - Node
categories: []
date: 2017-09-28 21:36:00
---
# basic

- objects are pass by reference, Basic types are pass by value

- If reference was no longer referenced, it would be collected by the GC of V8.

- If a value variable was inside a closure, it wouldn't be release until the closure was no longer referenced.

- In non-closure scope, it will be collected when V8 is switched to new space.

```
// memory leak analysis
https://zhuanlan.zhihu.com/p/25736931
```

## Q
- 0.1 + 0.2 === 0.3? Why?

# module

- clean `require.cache` to hot reload js module

- nodejs wrap module

```javascript
// b.js
(function (exports, require, module, __filename, __dirname) {
  t = 111;
})();

// a.js
(function (exports, require, module, __filename, __dirname) {
  console.log(t); // 111
})();
```

```
// x-y problem
https://coolshell.cn/articles/10804.html
```

## Q
- will a.js & b.js require each other cause to crash?