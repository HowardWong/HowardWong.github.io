title: OOP in Javascript
author: Howard Wong
date: 2018-03-08 11:47:34
tags:
---
# Object

## define an object

### Description API

```javascript
{
	configurable: boolen | false,
    enumrable: boolean | false,
    	(affect for-in, Object.keys, JSON.stringify)
    writable: boolean | false,
    get: function | undefined,
    set: function | undefined,
    value: any | undefined 
}
```

### Object.defineProperty

```javascript
// Cannot both specify accessors and value / writable

let bValue = 38;
Object.defineProperty(o, 'b', {
  get() { return bValue; },
  set(newValue) { bValue = newValue; },
  enumerable: true,
  configurable: true
});
```

### Object.defineProperties

```javascript
Object.defineProperties(object, {
  property1: {
    value: 42,
    writable: true
  },
  property2: {}
});
```

## Object Methods

### Object.getOwnPropertyNames
```javascript
// including non-enumerable properties except for those which use Symbol
const object = {
  a: 1,
  b: 2,
  c: 3
};

console.log(Object.getOwnPropertyNames(object));
// expected output: Array ["a", "b", "c"]

```

### Object.getOwnPropertyDescriptor

```javascript
// 获取属性描述
Object.getOwnPropertyDescriptor(object, 'property');
// property: {
//   value: 42,
//   writable: true
// }
```

### Object.getPropertyOf
```javascript
const prototype = {};
const object = Object.create(prototype);

Object.getPrototypeOf(object) === prototype; // true
```

### Object.create
```javascript
// Shape - superclass
function Shape() {
  this.x = 0;
  this.y = 0;
}

// superclass method
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};

// Rectangle - subclass
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// subclass extends superclass
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();

console.log('Is rect an instance of Rectangle?',
  rect instanceof Rectangle); // true
console.log('Is rect an instance of Shape?',
  rect instanceof Shape); // true
rect.move(1, 1); // Outputs, 'Shape moved.'
```
```javascript
// polyfill
if(typeof Object.create !== 'function') {
	Object.create = proto => {
    	function F() {};
        F.prototype = proto;
		return new F();
    }
}
```

### Object.prototype.hasOwnProperty
```javascript
// whether the object has the specified property as own (not inherited) property

const object1 = new Object();
object1.property1 = 42;

object1.hasOwnProperty('property1'); // true
object1.hasOwnProperty('toString'); // false
object1.hasOwnProperty('hasOwnProperty'); // false
```

### Object.prototype.isPropertyOf
```javascript
function object1() {}
function object2() {}

object1.prototype = Object.create(object2.prototype);

const object3 = new object1();

object1.prototype.isPrototypeOf(object3); // true

object2.prototype.isPrototypeOf(object3); // true
```

## Entends

```
ECMAScript使用原型链做为实现继承的方法，利用原型使一个引用类型继承另一个引用类型的属性和方法
```

- 每个构造函数都有一个原型对象
- 每个原型对象的constructor都执行构造函数
- 实例包含一个指向原型对象的内部指针

