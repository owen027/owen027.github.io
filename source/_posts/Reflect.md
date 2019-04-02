---
title: Reflect
date: 2019-04-02 16:22:02
categories: 
- ES6
tags:
- Reflect
---

## Reflect

> 将`Object`对象中一些属于语言内部的方法，放到 `Reflect`对象中
> 优化一些放法返回的结果
>只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法,不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为。

### 静态方法
> 13 个静态方法  作用对应Object同名方法
###  Reflect.apply(target, thisArg, args)
>等同于 `Function` 构造函数中的`Function.prototype.apply.call(func, thisArg, args)`
```javascript
const ages = [11, 33, 12, 54, 18, 96];

// ES5
const youngest = Math.min.apply(Math, ages);
const oldest = Math.max.apply(Math, ages);
const type = Object.prototype.toString.call(youngest);

// Reflect
const youngest = Reflect.apply(Math.min, Math, ages);
const oldest = Reflect.apply(Math.max, Math, ages);
const type = Reflect.apply(Object.prototype.toString, youngest, []);
```
### Reflect.construct(target, args)
> 对应 `new target(...args)`  提供了一种不使用new，来调用构造函数的方法。
```javascript
function Greeting(name) {
  this.name = name;
}

// new 的写法
const instance = new Greeting('Owen');

// Reflect.construct 的写法
const instance = Reflect.construct(Greeting, ['Owen']);
```
### Reflect.get(target, name, receiver)
> 查找目标对象属性值并返回
```javascript
let obj = {
    name : 'Owen',
    age:18,
    get baz(){
      return this.name + this.age 
    }
}

Reflect.get(obj,'name') //'Owen'
Reflect.get(obj,'baz')  //'Owen18'

// receiver 改变 get 函数 this 指向
let obj1 ={
     name : 'Owen',
    age:16,
}
Reflect.get(obj,'baz',obj1) //'Owen16
```
### Reflect.set(target, name, value, receiver)
```javascript
let obj = {
    name : 'Owen',
    age:18,
    set baz(val){
      return this.age = val;
    }
}
Reflect.set(obj,'name','wen') 
obj.name //'wen'
Reflect.set(obj,'baz', 16)
obj.age // 16

//receiver 改变 set this 指向
let obj1 = {
  age
}
```
> 如果 `Proxy `和 `Reflect` 联合使用，<label>并且传入 `receiver` </label>， 那么 `Reflect.set` 会触发`Proxy.defineProperty `
```javascript
let proxy = {
    name:'Owen'
}
let handler = {
    set (target, key, value, receiver){
        console.log('set')
        Refelect.set(target, key, value, receiver)
    },
    defineProerty(target, key, attribute){
        console.log('defineProperty')
        Reflect.defineProperty(target, key, attribute)
    }
}

let obj = new Proxy(proxy,handler)
obj.name = 'wen';
// set
// defineProperty
/* 
因为Proxy.set的receiver参数总是指向当前的 Proxy实例（即上例的obj），
而Reflect.set一旦传入receiver，就会将属性赋值到receiver上面（即obj），导致触发defineProperty拦截。
如果Reflect.set没有传入receiver，那么就不会触发defineProperty拦截。
*/
```
- Reflect.defineProperty(target, name, desc)
### Reflect.deleteProperty(target, name)
> 对应 `delete obj.name`
```javascript
let obj = {
    name:'Owen'
}
//es5 
delete obj.name

// Reflect
Reflect.deleteProperty(obj,'name')
```
### Reflect.has(target, name)
> 对应 `name in obj`  中的 in 运算
```javascript
let obj = {
    name:'Owen'
}
//es5
 'name' in obj // ture

//Reflect
Reflect.has(obj,'name') //true
```

- Reflect.ownKeys(target)
- Reflect.isExtensible(target)
- Reflect.preventExtensions(target)
- Reflect.getOwnPropertyDescriptor(target, name)
### Reflect.getPrototypeOf(target)
> 获取对象原型
> 参数不是对象，Object.getPrototypeOf会将这个参数转为对象，然后再运行，而Reflect.getPrototypeOf会报错。
```javascript
const myObj = new FancyThing();

// es5 
Object.getPrototypeOf(myObj) === FancyThing.prototype;

// Reflect
Reflect.getPrototypeOf(myObj) === FancyThing.prototype;

```
- Reflect.setPrototypeOf(target, prototype)
