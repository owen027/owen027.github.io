---
title: JavaScript OOP(三)
date: 2019-03-05 23:36:51
categories:
- JavaScript
tags: 
- OOP
---

### 对象继承
A 对象通过继承 B 对象，就能直接拥有 B 对象的所有属性和方法。这对于<label>代码的复用</label>是非常有用的。

JavaScript 语言的继承不通过 class，而是通过“原型对象”`prototype`实现
#### 传统原型链式继承
- 过多的继承属性
- 比如一个函数用不到某个原型方法或属性，那么方法或属性就过剩了
```javascript
function Grand(){};
Grand.prototype.name="grand";

let grand = new Grand();

Father.prototype=grand;
function Father(){}
let father = new Father();

Son.prototype=father;
function Son(){}
let son = new Son();
```

####  借用构造函数  使用call/appply 
- 不是真正继承，因为不能调用原型上的方法，而且每调用一次都会调用多次函数，实际上步骤没有变少
- 工业级推荐使用
- 缺点无法添加私有原型

```Javascript
 function Father() { }

function Son() {
  Father.call(this); // 调用父类构造函数
}

Son.prototype.print = function() {
  Father.prototype.print.call(this);//只使用单个方法
}
// 子类继承父类的原型
Son.prototype = Object.create(Father.prototype);
Son.prototype.constructor = Son;
```

#### 共有原型
```javascript

Father.prototype ={
    name:"common"
}
function Father(){}
Son.prototype =  Object.create(Father.prototype)
function Son(){}

function inherit (Target,Origin){//继承源
  Target.prototype = Origin.prototype;
}

```

####  圣杯模式
>隐式附加的东西就私有化，可以公共定义的东西提取出来公有化
```JavaScript
let inherit =(function(){
    let Interim =function Interim() {};
   return  function (Target,Origin){//继承源
   Interim.prototype =Object.create(Origin);
   Target.prototype = Interim.prototype;
  
  //现在 可以 制定自己的私有属性，但是 constuctor 不是 原函数所一手动赋值回来，如果想要知道函数真正继承那个原型需要保存它
  Target.prototype.constuctor = Target;
  Target.prototype.yliluokka =Origin;
    }
  }())

```
#### 多重继承 
JavaScript 不提供多重继承功能，即不允许一个对象同时继承多个对象。但是，可以通过`Object.assign`，实现这个功能。这种模式称之为 Mixin (混入)
```javascript
function Fn1(){ }
function Fn2(){ }
function Son(){
  F1.call(this);
  F2.call(this);
 }
//继承F1
Son.prototype =Object.create(Fn1.prototype);
//继承F2
Object.assign(Son.prototype,Fn2.prototype);
Son.prototype.constructor =Son;
let a =new Son();

```

### call，apply and bind
> 都可改变函数内部this的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。
 #### call and apply

```JavaScript
function test() {} 
//test() == test.call()

let obj ={};
Object.prototype.toString.call(obj) //"[object Object]"
//因为call 和 apply 会将函数中的this指向第一个参数
//相当于 obj.toString()

```

`call and apply` 二者区别在于传参：
- call 第二个参数开始单个单个参数传
- apply 第二个参数为数组或类数组
```javascript
//返回数组中最大的数
let a = [1, 2, 4, 1, 15];
Math.max.apply(null, a) // 15

//将数组的空元素变为undefined
Array.apply(null, [1,,3,,4])//[1,undefined,3,undefined,4];

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

bind方法用于将函数体内的this绑定到某个对象，然后返回一个新函数。

```javascript
let counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

let func = counter.inc.bind(counter);
func();
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
newAdd(1,5)//12
```

 <label>第一个参数是null或undefined，等于将this绑定到全局对象</label>
 #### bind方法使用注意点
- bind方法每运行一次，就返回一个新函数 需要一个变量接收

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

###  `Object 系统默认方法`

 - `getPrototypeOf` 获取对象原型,只有一个参数
 ```javascript
   function Foo (){}
   let obj = new Foo ();
   Object.getPrototypeOf(obj) // Foo.prototype

   //空对象原型
    Object.getPrototypeOf({}) // Object.prototype
   // Object.prototype 原型
    Object.getPrototypeOf(Object.prototype) //null
   // Foo
   Object.getPrototypeOf(Foo) // Function.prototype

   ```
 - `setPrototypeOf` 设置对象原型 
   有两个参数：
    1. 现有对象
    2. 继承的原型对象
 ```javascript
 let now = {};
 let pro = {name:"Owen"};

 Object.setPrototypeOf(now,pro);
 now.name //"Owen"

 ```

#### `Object.create()` 
> 生成实例对象的常用方法 参数必须为对象 或 null
  - 参数为 `null` 会生成一个不会继承任何属性和方法的对象
```javascript
  let obj = Object.create(null);
  obj.toString()// Error

    //会继承第二个参数的属性和方法
  let obj = Object.create({}, {
      p1: {
        value: 123,
        enumerable: true,
        configurable: true,
        writable: true,
      },
      p2: {
        value: 'Owen',
        enumerable: true,
        configurable: true,
        writable: true,
      }
    });

  // 等同于
  let obj = Object.create({});
  obj.p1 = 123;
  obj.p2 = 'Owen';

  //生成的对象会继承它的原型对象的构造函数。
  function Foo() {}
  let f = new Foo();
  let b = Object.create(f);

  b.constructor === Foo // true
  b instanceof Foo // true

```

#### `object.isPrototypeOf` 
 > 判断对象是否再参数对象的原型链上

```javascript
 function F(){}
 let f = new F()
 F.prototype.isPrototypeOf(f) //true
```
#####  获取原型的三种方法
 1. `obj.__proto__`
 2. `obj.constructor.prototype`
 3. `Object.getPrototypeOf(obj)`

 - 前两种不可靠，都个一手动修改， 而且 `__proto__` 只有浏览器才需要部署

 #### `getOwnPropertyNames` 和 `keys` 
 > 以数组形式返回参数对象所有属性名(不包含继承属性)
```javascript
//不管可不可遍历都会返回出来
Object.getOwnPropertyNames(Date);//["length", "name", "prototype", "now", "parse", "UTC"]
//返回可遍历属性
Object.keys(Date)// []
```

#### `hasOwnProperty` 
 > 判断参数是否是自身的属性，唯一一个不会遍历原型链的方法
```javascript
Array.hasOwnProperty('length')//true
```
### 拷贝对象

拷贝对象需要确保两件事情：
- 与原对象具有同样的原型。
- 与原对象具有同样的实例属性。

```javascript

function copyOwn (target,origin){
    Object.getOwnPropertyNames(origin).forEach((key)=>{
      let desc =Object.getOwnPropertyDescriptor(origin,key);
      Object.defineProperty(target,origin,desc);
    })
    return target
}

function copy(origin){
  let clone = Object.create (Object.getPrototypeOf(origin));
    copyOwn(clone,origin)
  return clone
}

//es8
const copyTwo = origin =>Object.create( Object.getPropertyOf(origin),Object.getOwnPropertyDescriptor(origin) );

```