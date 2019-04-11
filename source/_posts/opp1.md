---
title: JavaScript OOP(一)
date: 2019-03-04 23:25:54
categories:
- JavaScript
tags: 
- OOP
---

###  什么是对象 
 
对象是单个实物的抽象，通常需要一个模板，表示某一类实物的共同特征，然后对象根据这个模板生成。
   面向对象编程的第一步，就是要生成对象。
 对象是一个容器，封装了属性（property）和方法（method），属性是对象的状态，方法是对象的行为（完成某种任务）。
比如，我们可以把动物抽象为animal对象，使用“属性”记录具体是那一种动物，使用“方法”表示动物的某种行为（奔跑、捕猎、休息等等）。

> 典型的面向对象编程语言（比如 C++ 和 Java），都有“类”（class）这个概念。所谓“类”就是对象的模板，对象就是“类”的实例。

JavaScript 语言的对象体系，不是基于“类”的，而是基于构造数`constructor`和原型链`prototype`；
所以JS <label>专门使用构造函数作为对象模板</label>；一个构造函数，可生成多个实列对象，它们有相同的结构

#### 构造函数与普通函数区别

- 构造函数就是一个普通的函数，但是有自己的特征和用法。
- 函数体内部使用了this关键字，代表了所要生成的对象实例。
- 生成对象的时候，必须使用new命令。
```javascript  
//constructor 
var Bird = function () {
  this.name = 'lai fu';
};
var bird1 = new Bird(); // 也可以使用 new Bird; 推荐使用前者
console.log(bird1.name) // "lai fu"

//ordinary
var a =Bird();
console.log(a) // undefined
console.log(a.name) // typeError
name // 'laifu'
```

#### 防止把构造函数`constructor`当普通函数使用

```javascript  
 //使用 严格模式
function Fubar(foo, bar){
  'use strict';
  this._foo = foo;
  this._bar = bar;
}

Fubar()// TypeError

//判断 this 不是构造函数（constructor）的实列对象 那么手动返回自身constructor

function Far(a){
    if (!(this instanceof Far)) return new Far(a);
    
    this._a=a;
}
Far(1)._a 
```

### `new`命令的原理

- 创建一个空对象，作为将要返回的对象实例。
- 将这个空对象的原型，指向构造函数的prototype属性。
- 将这个空对象赋值给函数内部的this关键字。
- 开始执行构造函数内部的代码。
 
```javascript   
/**
*新生成一个空对象
*链接到原型
*绑定 this
*返回新对象
**/
function _new(constuctor,param) {
  
    // 获得构造函数
    let Con = [].shift.call(arguments);
    // 链接到原型
  let obj = Object.create(Con.prototype);
    // 绑定 this，执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return (typeof(result) === 'object' && result != null)  ? result : obj
}
 var fn = _new(
   function Person (name,age){
   this.name = name; this.age = age
    }, 'Owen', 28);
fn.name // 'Owen'
```
### new.target

```javascript   
function f() {
  console.log(new.target === f);
}

f() // false
new f() // true 
//可利用 它来判断是否使用 new 命令

function f() {
  if (!new.target) {
    throw new Error('请使用 new 命令调用！');
  }
  // ...
}

f() // Uncaught Error: 请使用 new 命令调用！
```

---

### `this`实质

> 原始的对象以字典结构保存，每一个属性名都对应一个属性描述对象。

```javascript 
var obj = { a:  1 };
```
JavaScript 存储变量实际上是以下面的形式保存的。

```JavaScript   
{
  a: {
    [[value]]: 1 //函数的地址
    [[writable]]: true //是否可赋值
    [[enumerable]]: true//是否可枚举
    [[configurable]]: true//是否可配置
  }
}
```
<label>属性的值保存在属性描述对象的value属性里面。</label>
 如果 a 属性的值是<label>引用值</label> 那么属性将以下面的形式保存的：
```JavaScript   
var obj = { fn: function () {} };
/*
{
  fn: {
    [[value]]: 
    [[writable]]: true 
    [[enumerable]]: true 
    [[configurable]]: true 
  }
}
*/
```
由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行。

``` JavaScript   
var f n= function () {};
var obj = { f: fn };

// 单独执行
 fn()

// obj 环境执行
obj.f()

```
> JavaScript 允许在函数体内部，引用当前环境的其他变量。

由于函数可以在不同的运行环境执行，所以需要有一种机制，能够在函数体内部获得当前的运行环境（context）。所以，this就出现了，它的设计目的就是在函数体内部，指代<label>函数当前的运行环境</label>。


下面这几种用法，都会改变this的指向。
``` javascript   

(obj.fn = obj.fn)() // window
// 等同于
(function () {
  console.log(this);
})()


(false || obj.fn)() // window
// 等同于
(false || function () {
  console.log(this);
})()


(4, obj.fn)() // window
// 等同于
(4, function () {
  console.log(this);
})()


```

数组调用forEach 方法时函数内部this 指向window ，将父级上下文传递给forEach 改变this指向
```javascript   
var o = {
  v: 'hello',
  p: [ 'Owen', 18 ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + '-' + item);
    }, this); //将外层的this传递给forEach方法
  }
}

o.f() // hello-Owen hello-18

```
end