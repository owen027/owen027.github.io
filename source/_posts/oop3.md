---
title: JavaScript OOP(三)
date: 2019-03-05 23:36:51
categories:
- JavaScript
tags: 
- oop
---

###对象继承
A 对象通过继承 B 对象，就能直接拥有 B 对象的所有属性和方法。这对于<label>代码的复用</label>是非常有用的。

JavaScript 语言的继承不通过 class，而是通过“原型对象”`prototype`实现
#### 传统原型链式继承
- 过多的继承属性
- 比如一个函数用不到某个原型方法或属性，那么方法或属性就过剩了
```javascript
function Grand(){};
Grand.prototype.name="grand";

var grand = new Grand();

Father.prototype=grand;
function Father(){}
var father = new Father();

Son.prototype=father;
function Son(){}
var son = new Son();
```

####  借用构造函数  使用call/appply 
- 不真正继承，因为不能调用原型上的方法，而且每调用一次都会调用多此函数，实际上步骤没有变少
- 工业级推荐使用
- 缺点无法添加私有原型
```javascript
//公有原型
Father.prototype ={
    name:"common"
}
function Father(){}
Son.prototype = Father.prototype ;
function Son(){}

function inherit (Target,Origin){//继承源
  Target.prototype = Origin.prototype;
}

```

#### 圣杯模式
>隐式附加的东西就私有化，可以公共定义的东西提取出来公有化
```JavaScript
var inherit =(function(){
    var Interim =function Interim() {};
   return  function (Target,Origin){//继承源
   Interim.prototype =Object.create(Origin);
   Target.prototype = Interim.prototype;
  
  //现在 可以 制定自己的私有属性，但是 constuctor 不是 原函数所一手动赋值回来，如果想要知道函数真正继承那个原型需要保存它
  Target.prototype.constuctor = Target;
  Target.prototype.yliluokka =Origin;
    }
  }())

```
### call，apply and bind
> 都可改变函数内部this的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。
 #### call and apply

```JavaScript
function test() {} 
//test() == test.call()

var obj ={};
Object.prototype.toString.call(obj) //"[object Object]"
//因为call 和 apply 会将函数中的this指向第一个参数
//相当于 obj.toString()

```

`call and apply` 二者区别在于传参：
- call 第二个参数开始单个单个参数传
- apply 第二个参数为数组或类数组
```javascript
//返回数组中最大的数
var a = [1, 2, 4, 1, 15];
Math.max.apply(null, a) // 15

//将数组的空元素变为undefined
Array.apply(null [1,,3,,4)//[1,undefined,3,undefined,4];

```
空元素与undefined的差别
- forEach方法会跳过空元素，但是不会跳过undefined。因此，遍历内部元素的时候，会得到不同的结果。
- 
<label>转换类似数组的对象</label>

```
var obj={0: 1, length: 2}
Array.protetype.slice.apply(obj);//[1,undefined]

```
<label>被处理的对象必须有length属性，以及相对应的数字键。</label>


######  <label>参数为空、null和undefined，则默认传入全局对象。</label>
---

bind方法用于将函数体内的this绑定到某个对象，然后返回一个新函数。

```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

var func = counter.inc.bind(counter);
func();
counter.count // 1

var add = function (x, y) {
  return x * this.m + y * this.n;
}

var obj = {
  m: 2,
  n: 2
};

var newAdd = add.bind(obj, 5); //将x 绑定为 5 
newAdd(5) // 20
newAdd(1,5)//12
```

 <label>第一个参数是null或undefined，等于将this绑定到全局对象</label>
 #### bind方法使用注意点
- bind方法每运行一次，就返回一个新函数 需要一个变量接收

- 结合回调函数使用
```javascript
var counter = {
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

var slice = Function.prototype.call.bind(Array.prototype.slice);
Function.prototype.slice.call([1, 2, 3], 0, 1) // [1]
//slice([1, 2, 3], 0, 1) 

var push = Function.prototype.call.bind(Array.prototype.push);
var pop = Function.prototype.call.bind(Array.prototype.pop);

var a = [1 ,2 ,3];
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

var o = { v: 123 };
var bind = Function.prototype.call.bind(Function.prototype.bind);
bind(f, o)() // 123
```
