---
title: 设计模式基础（二）
date: 2019-07-08 13:13:34
categories:
- design patterns
tags:
- Pattern intro
---

# 原型模式
在以类为中心的面向对象编程语言中，类和对象的关系就像铸模和铸件的关系，对象总是从类中创建。而原型编程中，类不是必须的，对象未必从类中创建而来，可以拷贝另一个对象而变成新对象

> 从设计模式角度讲，原型模式是用于创建对象的一种模式，找到一个对象，不需要关心对象的具体类型，通过克隆来创建一个一模一样的对象。 原型不单是一种设计模式，也被称为编程泛型

原型模式实现的关键是语言本身是否提供了 clone 方法， ECMAScript 5 提供了 `Object.create` 方法
```javascript
function Plane (propertys){ //工厂方法
    this.blood = propertys.blood;
    this.attackLevel = propertys.attackLevel;
    this.defenseLevel = propertys.defenseLevel;
}
var plane = new Plane({
    blood :100,
    attackLevel :10,
    defenseLevel :10
});
var clonePlane = Object.create(plane);
console.log(clonePlane.__proto__) // Plane {blood: 100, attackLevel: 10, defenseLevel: 10}

```
通过克隆 `plane 对象`得到 `clonePlane 对象`,那么 plane 就被称为 clonePlane 的原型；而 plane 中所有的属性和方法都将挂载到`__proto__` 原型属性上

每个 JS 对象都有 `__proto__` 属性(隐式原型属性)，指向创建该对象的构造函数的原型,这个属性可以访问到原型（`[[prototype]]`） 内部属性。这个属性在现在来说已经不推荐直接去使用它了。


**由于 JavaScript 作为一门基于原型的语言，没有类的概念，每一个对象都是基于另一个对象的克隆，因此语言最初只有一个根对象 `Object.prototype`,如果A对象是从B对象克隆而来，那么B就是A的原型，如果C又是B的原型。它们之间就形成了一条原型链**

<label> **原型编程中有一个重要特性：访问对象中某个属性或方法，而对象本身没有对应属性和方法，那么它会从自己的原型链上查找，一直查到根对象为止**
</label>
**一级级向上，对性能有影响，寻找的层级越多，性能影响越大**

```javascript
console.log(clonePlane) // Plane {}

console.log(clonePlane.blood) // 100
console.log(clonePlane.color) // undefined

console.log(clonePlane.__proto__) //原型 Plane {blood: 100, attackLevel: 10, defenseLevel: 10}
```
上述代码中的`clonePlane 对象` 本身是没有 blood 和 color 属性，却能访问到blood的值而访问不到color的值，是因为 `clonePlane 对象` 原型链中有 blood 属性而没有color属性。

**原型编程基本规则：**
1. 所有数据都是对象
2. 对象会记住它的原型（类似于继承）
3. 要得到一个对象，需要将另一个对象作为原型并克隆它
4. 访问不到对象的某个属性或方法，那么它会从自己的原型链上查找，一直查到根对象为止

## 原型继承

在JavaScript中我们遇到的每一个对象都是从 `Object.prototype`克隆而来，通过`Object.getPrototypeOf`来判断对象的原型从属关系。
```javascript
let obj = new Object();
let obj1 = {};

console.log(Object.getPrototypeOf(obj) === Object.prototype) // true
console.log(Object.getPrototypeOf(obj1) === Object.prototype) // true

```
由于引擎内部会自动实现对 `Object.prototype` 的克隆，所有我们执行显示的使用`let obj = new Object(); let obj1 = {};`
 `prototype` 有一个属性 `constructor`，默认指向原型所在的构造函数
,`constructor `是一个公有且不可枚举的属性。一旦我们改变了函数的 prototype ，那么新对象就没有这个属性了,如果修改了原型对象，一般会同时修改constructor属性，防止引用的时候出错。

下面我们来看`new` 运算符

 <label> **JavaScript中没有类的概念，所以 `Object` 是一个构造函数，JavaScript 中的函数即可作为普通函数调用，又可作为构造器调用,当使用 new 运算调用函数时，函数就是构造器。**</label>

 ### `new`命令的原理

1. 创建一个空对象，作为返回的对象实例
2. 将构造函数指向原型`prototype`属性（继承构造函数）
3. 绑定作用域（ this 指向空对象 ）
4. 执行构造函数,如果构造函数return 为对象则使用该对象，否则 return 创建的空对象

模拟 `new` 运算
```javascript
function __new (fun,args){
  // 获取构造函数
  let arr =[...arguments]
  let Con = arr.shift()
  // 创建对象，将构造函数的原型指向它
  let obj = Object.create(Con.prototype)
  // 绑定 this
  let ret = Con.apply(obj,arr)
  return (typeof ret === 'object' && ret !== null )? ret : obj
}
let person = __new(function Person({ name,sex,age }){
    this.name = name;
    this.sex = sex
    this.age = age
},{name:'owen',sex:'man',age:18})
console.log(person) // Person {name: "owen", sex: "man", age: 18}
```
**new.target：**  可利用 它来判断是否使用 new 命令

```javascript
function func() {
  if (!new.target) {
    throw new Error('请使用 new 命令调用！');
  }
  // ...
}

func() // Uncaught Error: 请使用 new 命令调用！
```


### call，apply and bind
Javascript 编程中 `call，apply 和 bind` 方法被广泛运用，在学习设计模式前先理解这几个概念

> 三者都可改变函数内部this的指向（即函数执行时所在的作用域）

#### call and apply

> 都是在改变this 指向后调用该函数。
```JavaScript
function test() {}
//test() == test.call()

let obj ={};
Object.prototype.toString.call(obj) //"[object Object]"
//因为call 和 apply 会将函数中的this指向第一个参数
//相当于 obj.toString()

```

**`call and apply` 二者区别在于传参：**
- call 第二个参数开始单个单个参数传
- apply 第二个参数为数组或类数组

```javascript
//返回数组中最大的数
let arr = [1, 2, 4, 1, 15];
Math.max.apply(null, arr) // 15

//将数组的空元素变为undefined
Array.apply(null,[1,,3,,4])//[1,undefined,3,undefined,4];

```
空元素与undefined的差别
- forEach方法会跳过空元素，但是不会跳过undefined。因此，遍历内部元素的时候，会得到不同的结果。
- <label>转换类似数组的对象</label>

```javascript
let obj={0: 1, length: 2}
Array.protetype.slice.apply(obj);//[1,undefined]

```
<label>被处理的对象必须有length属性，以及相对应的数字键。</label>


######  <label>参数为空、null和undefined，则默认传入全局对象。</label>
---
#### bind

**bind方法用于将函数体内的this绑定到某个对象，然后返回一个新函数。**

```javascript
let counter = {
  count: 0,
  func: function () {
    this.count++;
  }
};

let func = counter.func.bind(counter);
func(); // 相当于 count++
counter.count // 1

let add = function (x, y) {
  return x * this.m + y * this.n;
}

let obj = {
  m: 2,
  n: 2
};

let newAdd = add.bind(obj, 5); //将x 绑定为 5
newAdd(5) // 20
newAdd(1,6)//12 6为起作用 相当于 5*2+1*2
```
<label>第一个参数是null或undefined，等于将this绑定到全局对象</label>

#### bind方法使用注意点
- bind方法每运行一次，就返回一个新函数 需要一个变量接收

#### 模拟 bind
```javascript

  Function.prototype.bind = function (...Args) {
    if (typeof this !== "function") {
      throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
    }
    let that = this, //保存原函数
    target = Args.shift(), //提取须绑定的对像
    func =  (...args) =>that.apply(target ? this : target || window, aArgs.concat(args));  //  aArgs.concat(args) 合并参数
    func.prototype = Object.create(this.prototype); // 继承原函数的原型
    return func;
  };

```

- 结合回调函数使用
```javascript
let counter = {
  count: 0,
  inc: function () {
    'use strict';
    this.count++;
  }
};

function callIt(callback) {
  callback();
}

callIt(counter.inc.bind(counter));
counter.count // 1
```
- 结合call方法使用

```javascript
[1, 2, 3].slice(0, 1) // [1]
// 等同于
Array.prototype.slice.call([1, 2, 3], 0, 1) // [1]


//将Array.prototype.slice变成Function.prototype.call方法所在的对象
//调用时就变成了Array.prototype.slice.call。
let slice = Function.prototype.call.bind(Array.prototype.slice);
Function.prototype.slice.call([1, 2, 3], 0, 1) // [1]
//slice([1, 2, 3], 0, 1)

let push = Function.prototype.call.bind(Array.prototype.push);
let pop = Function.prototype.call.bind(Array.prototype.pop);

let a = [1 ,2 ,3];
push(a, 4)
a // [1, 2, 3, 4]

pop(a)
a // [1, 2, 3]

```
- 将Function.prototype.bind方法变成Function.prototype.call的方法，就意味着bind的调用形式也可以被改写

```javascript
function f() {
  console.log(this.v);
}

let o = { v: 123 };
let bind = Function.prototype.call.bind(Function.prototype.bind);
bind(f, o)() // 123
```

