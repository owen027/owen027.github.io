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
> 会逐渐替换` Object`对象 中对应的方法
> 优化一些放法返回的结果
>只要是 Proxy 对象的方法，就能在 Reflect 对象上找到对应的方法,不管 Proxy 怎么修改默认行为，你总可以在 Reflect 上获取默认行为。

### 静态方法
> 13 个静态方法  作用对应Object同名方法
> <label>所有方法第一个参数，必须是对象，否则报错</label>
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
> 对应 `new target(...args)`  提供了一种不使用 new，来调用构造函数的方法。
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
  name: 'wen'
}
Reflect.set(obj,'baz', 16,obj1)
obj.name // wen

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
### Reflect.defineProperty(target, propertykey, attribute)
> 对应 `Object.defineProperty` 
```javascript 
function MyDate() {
  /*…*/
}

// es5
Object.defineProperty(MyDate, 'now', {
  value: () => Date.now()
});

// es6
Reflect.defineProperty(MyDate, 'now', {
  value: () => Date.now()
});
```
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

### Reflect.ownKeys(target)
> 对应 `Object.getOwnPropertyNames` and `Object.getOwnPropertySymbols`  返回对象所有属性
```javascript
let obj = {
  name:'Owen',
  age:18,
  [Symbol.for('name')]:'Owen',
  [Symbol.for('age')]:18
}

//es5
Object.getOwnPropertyNames(obj)
// ['name', 'age']

Object.getOwnPropertySymbols(obj)
//[Symbol(name), Symbol(age)]

//es6
Reflect.ownKeys(obj)
//['name', 'age',Symbol(name), Symbol(age)]

```
### Reflect.isExtensible(target)
> 对应 `Object.isExtensible`  返回 `boolean`，表示当前对象是否可扩展。
```javascript
const obj ={}
//es5
Object.isExtensible(obj) //true

//es6
Reflect.isExtensible(obj) //true
```
### Reflect.preventExtensions(target)
>  对应 `Object.preventExtensions`  让一个对象变为不可扩展。它返回 `boolean`，表示是否操作成功。
```javascript
const obj ={}
// es5 
Object.preventExtensions(obj) // Object {}

// es6
Reflect.preventExtensions(obj) // true

```
### Reflect.getOwnPropertyDescriptor(target, propertyKey) 
> 对应 `Object.getOwnPropertyDescriptors`   返回对象所有自身属性（非继承） 的描述对象
```javascript
var obj t = {};
Object.defineProperty(obj, 'hidden', {
  value: true,
  enumerable: false,
});

// es5
var theDescriptor = Object.getOwnPropertyDescriptor(obj, 'hidden');

// es6
theDescriptor = Reflect.getOwnPropertyDescriptor(obj, 'hidden');
```
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
#### Reflect.setPrototypeOf(target, prototype)
> 对应` Object.setPrototypeOf` 设置 原型对象， 返回 `Boolean`

```javascript
const obj = {};

function Person (){

}
//es5
Object.setPrototypeOf(obj,Person.prototype);

//es6 
Reflect.setPrototypeOf(obj,Person.prototype);

obj.length //0 
```

## Proxy Observer  Mode
> 函数字段观察数据对象，如果数据改变，函数自动执行

```javascript
const queue = new Set();

//队列函数
const observe = callback => queue.add(callback);

//观察目标
const observable = obj => new Proxy(obj,{set});

//自动执行
function set (target,key,value,receiver){
  const result = Reflect.set(target,key,value,receiver);
  queue.forEach(observer => observer() );
  return result
}



const person = observable({  
    name:'Owen',
    age:18
})


 function  auto(){
   console.log(`${person.name},${person.age}`);
 }


observe(auto);
person.name = 'Owen';

/* 
先定义一个Set集合，所有观察者函数都放进这个集合。然后，observable函数返回原始对象的代理，拦截赋值操作。
拦截函数set之中，会自动执行所有观察者。
 */
```
