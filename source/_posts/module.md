---
title: 模块化开发(一)
date: 2019-03-07 00:41:22
categories:
- JavaScript
tags:
- moduleDevelop
---

### 传统的模块化做法。
> 模块是实现特定功能的一组属性和方法的封装。

将模块写成一个对象，所有的模块成员都放到这个对象里面。
```javascript
var module1 = new Object({
  _count:0,
  f1:function(){},
  f2:function(){}
})
module1.f1()
module1.f2()
```
上面的对象可以改变里面的属性和方法，不安全

```javascript
var module1 = （function(){
 var count=0;
 return { 
   f1:function(){},
  f2:function(){}}
}());
module1.f1()
module1.f2()
module1.count //undefined
```
使用<label>立即执行函数</label> 将相应的方法和属性封装在函数中,这样就不会暴露私有成员
#### 利用构造函数封装对象
```javascript
function Father (){
  var arr =[];
  this.add = function (val){
       arr.push(val)
  }
  this.toString = function(){
    return arr.join('');
  }
}
var a = new Father();
a.add(1);//[1]
a.toString();//"1"
a.arr // undefined
```
上面的函数将 `arr` 变成私有变量，在函数外部无法访问，但是形成了闭包，非常耗费内存；
违背了构造函数与实例对象在数据上相分离的原则（即实例对象的数据，不应该保存在实例对象以外）。
```javascript
function ToString() {
  this._buffer = [];
}

ToString.prototype = {
  constructor: ToString,
  add: function (str) {
    this._buffer.push(str);
  },
  toString: function () {
    return this._buffer.join('');
  }
};
```
虽然上面的构造函数未生成闭包，但是外部可以修改方法和属性，不安全
#### 放大模式
如果一个模块很大或者一个模块需要继承另一个模块可以利用立即执行函数的特效来封装
```javascript
var module1 = (function(m1){
m1.m2=function(){
  console.log(this)
};
return m1;
}(window.modlue2 ||{})) //有些模块可能是null 确保函数正常执行 采用兼容模式 window.modlue2 ||{}
```
- 独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。
```javascript
var module1 = (function ($, swiper) {
　//...
}(jQuery, Swiper));
```
上面的 module1 引入 jQuery 和 Swiper 当做两个参数传入模块中，保证了模块的独立性，还使得模块之间的依赖关系变得明显。

**立即执行函数还可以起到命名空间的作用。**
```javaScript
(function($, window, document) {

  function go(num) {
  }

  function handleEvents() {
  }

  function initialize() {
  }

  function dieCarouselDie() {
  }

  //attach to the global scope
  window.finalCarousel = {
    init : initialize,
    destroy : dieCarouselDie
  }

}( jQuery, window, document ));

```
以上都有一个共同点:使用单个全局变量箭头代码包装在函数中，使用闭包建立私有空间
但是都有缺点：
 - 不知道模块(库) 的加载顺序
 - 还是有可能引起命名冲突，比如两个库都有相同的名称，或者使用哪个版本
有两种流行且实施良好的方法：CommonJS和AMD。可以解决上面的缺陷
 ### CommonJS 
> CommonJS 本质上是可复用的JavaScript,它导出特定的对象，提供其它程序使用。
 每个JavaScript 文件 都将模块存储在自己独有的作用域中。需要使用 `module.expots`来导出对象，并在需要它的程序中使用 `require('module')` 导入
 ```javascript
 //文件1
 function myModule() {
  this.hello = function() {
    return 'hello!';
  }

  this.goodbye = function() {
    return 'goodbye!';
  }
}

module.exports = myModule;


 //文件2
 var myModule = require('myModule');

var myModuleInstance = new myModule();
myModuleInstance.hello(); // 'hello!'
myModuleInstance.goodbye(); // 'goodbye!'
 ```
**这种方法的好处：**
  - 避免全局污染
  - 明确依赖项目
  - 语法清晰
  但是有个缺点 因为 `CommonJS` 采用服务器优先方法并且同步加载模块，在浏览器中使用它会阻止浏览器运行其他内容，直到加载完成。 
  我们可以使用 `AMD` 来异步加载
 ### AMD
 ```JavaScript
 define(['myModule', 'myOtherModule'], function(myModule, myOtherModule) {
  console.log(myModule.hello());
});
 ```
 上面的 `define` 函数将每个模块的依赖项，以数组的形式作为第一个参数。
  - 这些依赖项会在后台异步加载，一旦加载完成，`define` 函数就调用模块给出的回调函数
   `myModule` 可能像下面一样定义：

```javascript
   define([], function() {

  return {
    hello: function() {
      console.log('hello');
    },
    goodbye: function() {
      console.log('goodbye');
    }
  };
});

```
<label> `AMD` 和 `CommonJS` 不同点：</label>
AMD：
 - 采用浏览器优先的方法，异步加载
 - 先加载依赖项
 - 依赖项可以说 对象、函数、构造函数、字符串等等其他JS类型
CommonJS:
 - 采用服务器优先的方法，同步加载
 - 支持对象作为模块
 共同点： 先加载依赖项
 
###  通用模块定义 `UMD`
> 同时支持 `AMD `和 `CommonJS`
> 本质 创建了一种方法来使用两者的任何一种，同时支持全局变量定义，(JS兼容性的常用思想)所以 `UMD` 可以在客户端和服务器上工作
```javascript
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
      // AMD
    define(['myModule', 'myOtherModule'], factory);
  } else if (typeof exports === 'object') {
      // CommonJS
    module.exports = factory(require('myModule'), require('myOtherModule'));
  } else {
    // Browser globals (Note: root is window)
    root.returnExports = factory(root.myModule, root.myOtherModule);
  }
}(this, function (myModule, myOtherModule) {
  // Methods
  function notHelloOrGoodbye(){}; // A private method
  function hello(){}; // A public method because it's returned (see below)
  function goodbye(){}; // A public method because it's returned (see below)

  // Exposed public methods
  return {
      hello: hello,
      goodbye: goodbye
  }
}));
```

### ES6模块
> `CommonJS`、`AMD`相比，ES6模块的优点在于:紧凑和声明性语法和异步加载，以及更好的支持循环依赖等附加优势。
```javascript
// lib/counter.js

var counter = 1;

function increment() {
  counter++;
}

function decrement() {
  counter--;
}

module.exports = {
  counter: counter,
  increment: increment,
  decrement: decrement
};


// src/main.js

var counter = require('../../lib/counter');

counter.increment();
console.log(counter.counter); // 1
```

