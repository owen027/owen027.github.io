---
title: proxy
date: 2019-03-26 22:51:32
categories: 
- ES6
tags:
- Proxy
---

## Proxy
> 用于修改某些操作的默认行为。（对编程语言进行编程）
> 在目标对象之前架设一层‘门户’，外界访问对象时，必须先访问这个门户，<label>对外界的访问进行过滤和改写</label>

**生成：**
>ES6中 所有proxy实例 通过 Proxy 构造函数生成
```javascript
  new Proxy(target,handler);
// target对象 要访问的对象， handler对象 拦截行为
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
```
### Proxy 支持的操作

- get(target, propKey, receiver)：拦截对象属性的读取，比如proxy.foo和proxy['foo']。
```javascript
/**
*get 接收三个参数
*target 目标对象
*property 属性名
*proxy 实例本身
**/
var obj = {
    name:"Owen"
};
var proxy = new Proxy(obj, {
  get: function(target, property) {
    if (property in target) {
      return target[property];
    } else {
      throw new ReferenceError("Property \"" + property + "\" does not exist.");
    }
  }
});

proxy.name // "Owen"
proxy.age // error:Property "age" does not exist.
//如果访问目标对象不存在的属性，会抛出一个错误。如果没有这个拦截函数，访问不存在的属性，只会返回undefined。
```

 
- set(target, propKey, value, receiver)：拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
```javascript
/**
*set 有4 个参数
*target 目标对象
*property 属性名
*value 属性值
*proxy 实例本身
 */
 let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 100) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 对于满足条件的 age 属性以及其他属性，直接保存
    obj[prop] = value;
    return true
    //严格模式下 return false or undefined 会报错 

  }
};

let obj = new Proxy({}, validator);
obj.age = 18;
obj.age ='18'; //error
obj.age =101;//error
//每当对象发生变化时，会自动更新 DOM。

```
- has(target, propKey)：拦截propKey in proxy的操作，返回一个布尔值。
- deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
- ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
- getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
- defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
- preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
- getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
- isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
- setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- apply(target, object, args)：拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
```javascript
/**
*接收三个参数 
*target 目标对象
*this  目标对象上下文
*目标对象的参数数组
*/
var target = function () { return 'I am the target'; };
var handler = {
  apply: function () {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);

p()
// "I am the proxy"

```
- construct(target, args)：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。