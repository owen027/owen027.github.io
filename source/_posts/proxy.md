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

**生成:**
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

#### get(target, propKey, proxy)
> 拦截对象属性的读取，比如proxy.foo和proxy['foo']。
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

 
#### set(target, propKey, value, proxy)
> 拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
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
#### has(target, propKey)
> 拦截propKey in proxy的操作，返回一个布尔值。
```javascript
/**
*接收两个参数
*target目标对象
*key 查询的属性名
**/
var handler = {
  has (target, key) {
    if (key[0] === '_') {
      return false;
    }
    return key in target;
  }
};
var target = { _prop: 'Owen' };
var proxy = new Proxy(target, handler);
'_prop' in proxy // false
//拦截以‘_’开头的属性名 对for ...in 遍历不生效
```
#### deleteProperty(target, propKey)
> 拦截delete proxy[propKey]的操作，返回一个布尔值。
```javascript
var handler = {
  deleteProperty (target, key) {
    invariant(key, 'delete');
    delete target[key];
    return true;
  }
};
function invariant (key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  }
}

var target = { _prop: 'foo' };
var proxy = new Proxy(target, handler);
delete proxy._prop
// Error: Invalid attempt to delete private "_prop" property
```
#### ownKeys(target)
> 拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
```javascript
//拦截对象自身属性的读取操作
let target = {
  _bar: 'foo',
  _prop: 'bar',
  prop: 'Owen'
};

let handler = {
  ownKeys (target) {
    return Reflect.ownKeys(target).filter(key => key[0] !== '_');
  }
};

let proxy = new Proxy(target, handler);
for (let key of Object.keys(proxy)) {
  console.log(target[key]);
}
//会自动过滤 不存在的属性，Symbol，不可遍历属性
```
#### getOwnPropertyDescriptor(target, propKey)
> 拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
```javascript
var handler = {
  getOwnPropertyDescriptor (target, key) {
    if (key[0] === '_') {
      return;
    }
    return Object.getOwnPropertyDescriptor(target, key);
  }
};
var target = { _foo: 'bar', baz: 'tar' };
var proxy = new Proxy(target, handler);
Object.getOwnPropertyDescriptor(proxy, 'wat')
// undefined
Object.getOwnPropertyDescriptor(proxy, '_foo')
// undefined
Object.getOwnPropertyDescriptor(proxy, 'baz')
// { value: 'tar', writable: true, enumerable: true, configurable: true }
```
#### defineProperty(target, propKey, propDesc)
> 拦截 Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
```javascript
var handler = {
  defineProperty (target, key, descriptor) {
    return false;
  }
};
var target = {};
var proxy = new Proxy(target, handler);
proxy.owen = 'Owen' // 不会生效

```

#### preventExtensions(target)
> 拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
>该方法必须返回一个布尔值，否则会被自动转为布尔值。
>目标对象不可扩展时（<label>即Object.isExtensible(proxy)为false</label>），`proxy.preventExtensions`才能返回true
```javascript
var proxy = new Proxy({}, {
  preventExtensions: function(target) {
    return true;
  }
});

Object.preventExtensions(proxy)
//error
```
####  getPrototypeOf(target)
> 拦截Object.getPrototypeOf(proxy)，返回一个对象。
```javascript
//主要拦截获取对象原型
/** 
*Object.prototype.__proto__
*Object.prototype.isPrototypeOf()
*Object.getPrototypeOf()
*Reflect.getPrototypeOf()
*instanceof
**/
var proto = {};
var p = new Proxy({}, {
  getPrototypeOf(target) {
    return proto;
  }
});
Object.getPrototypeOf(p) === proto // true
//返回值必须是对象或者null，否则报错
```
#### isExtensible(target)
> 拦截Object.isExtensible(proxy)，返回一个布尔值。
```javascript
var p = new Proxy({}, {
  isExtensible: function(target) {
    console.log("Owen");
    return true;
  }
});

Object.isExtensible(p)
//返回值必须是Boolean
```
#### setPrototypeOf(target, proto)
> 拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
```javascript
var handler = {
  setPrototypeOf (target, proto) {
    throw new Error('Changing the prototype is forbidden');
  }
};
var proto = {};
var target = function () {};
var proxy = new Proxy(target, handler);
Object.setPrototypeOf(proxy, proto);
// Error: Changing the prototype is forbidden
```
#### apply(target, object, args)
> 拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
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
#### construct(target, args)
> 拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
```javascript
//接收两个参数
/**
*target 目标对象
*args 构造函数参数对象
函数必须返回对象，否则会报错
 */
 let obj = {
      construct: function(target, args) {
    console.log('Owen: ' + args.join(', '));
    return { value: args[0] * 10 };
   // retirn '18'; error
  }
 }
 var p = new Proxy(function () {}, obj)
 new p(2).value // 20
```

### Proxy.revocable 
> `Proxy.revocable`方法返回一个可取消的 Proxy 实例。
> 使用场景： 目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。
```javascript
let target = {}
let handler = {}

let {proxy, revoke} = Proxy.revocable(target,handler);
/* 
Proxy.revocable(target, handler);
{proxy: Proxy, revoke: ƒ}
*/
proxy.name = 'Owen'
proxy.name

revoke() //取消 proxy 实例
proxy.name 
//error
```
### Proxy this
 <label> 在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。 </label>

 ```javascript
 const target = new Date();
const handler = {};
const proxy = new Proxy(target, handler);

proxy.getDate();
//error


```

### Proxy 实例使用

```javascript

const service = createWebService('http://example.com/data');

service.employees().then(json => {
  const employees = JSON.parse(json);
  // ···
});

function createWebService(baseUrl) {
  return new Proxy({}, {
    get(target, propKey, receiver) {
      return () => httpGet(baseUrl+'/' + propKey);
    }
  });
}

```