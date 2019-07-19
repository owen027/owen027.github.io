---
title: 闭包（closure）
date: 2019-06-27 14:15:07
categories:
- JavaScript
tags:
- closure
---


# 什么是闭包（closure）？
在理解闭包前，须理解变量[作用域](https://zh.wikipedia.org/zh-hans/%E4%BD%9C%E7%94%A8%E5%9F%9F)。作用域分全局和局部作用域，是指代码中定义变量的区域。(变量有效访问的范围。)

**静态作用域**
> 静态作用域又叫做词法作用域，采用词法作用域的变量叫词法变量。词法变量有一个在编译时静态确定的作用域。(JavaScript 采用的是词法作用域) 词法变量的作用域可以是一个函数或一段代码，该变量在这段代码区域内可见（visibility）；在这段区域以外该变量不可见（或无法访问）。词法作用域里，取变量的值时，会检查函数定义时的文本环境，捕捉函数定义时对该变量的绑定。

**动态作用域**
> 动态作用域的变量叫做动态变量。程序正在执行定义了动态变量的代码段，那么在这段时间内，该变量一直存在；代码段执行结束，该变量便消失。(函数的作用域在函数调用时才决定。)动态作用域里，取变量的值时，会由内向外逐层检查函数的调用链，并打印第一次遇到的那个绑定的值。显然，最外层的绑定即是全局状态下的那个值。

变量无权访问子作用域，只能访问自己和父级以上的作用域

```javascript
var name = "Owen"; // 全局变量
var sex = "Man";
function person(){
    console.log(name) // "Owen"
    var age = "18"
    var sex = "man";
    console.log(sex) // "man"  优先访问 局部变量 sex
}
person()
console.log(age) //   age is not defined  无法访问 person 中的 age 局部变量

```


## 预编译

当函数执行时，会创建一个执行期上下文(即作用域)的对象AO(存储在`[[scope]]`中), 一个新的AO指向 定义了一个函数执行时的环境。
函数执行时对应的AO, 是独一无二的，每次调用函数就创建一个OA， 函数执行完毕 AO的指向就会销毁

`[[scope]]`: 每个函数都是对象，对象中有些属性可访问，有些不可以，`[[scope]]`就不可访问，它存储了运行期上下文的集合(`[GO,AO]`)。
作用域链：就是`[[scope]]`中所存储的AO对象集合，呈链式链接

### 查找变量：

函数刚定义就存储了 所在环境的执行期上下文,执行时 创建自己的AO

```javascript
function fun (a){
    console.log(a);// function
    var a=123;
    function a (){};
      console.log(a)// 123  函数声明已提升所以不用管
    var b =function(){};
    console.log(b);//function 因为是函数表达式，只提升了 变量b， 这样的函数体不会提升
}
fun(1); //函数 123 123 函数
/*
代码执行
1. 会创建 一个（全局为GO）AO(Activation  Object)对象（执行期上下文/作用域）一个存储空间库

2. 找形参和变量声明，将形参和变量名作为AO的属性名，值为undefined,重复的只用写一个

3. 将实参值和形参统一

4. 在函数体里找函数声明，值为函数体

5. if/return 不用管 声明还是会提升
AO{
   a:function a (){};
   b:undefined
}
记住函数是一等公民权限最高

*/
```
**函数内部声明变量的时候，一定要使用var，let or const命令。如果不用的话，你实际上声明了一个全局变量！**

### 变量的生命周期
**内存生命周期：**
1. 分配你所需要的内存

2. 使用分配到的内存（读, 写）

3. 不需要时将其释放/归还

**全局变量可永久访问，除非主动销毁，而局部变量在函数运行结束时就会随之销毁，当局部变量还能被外界访问时，将会保留，不被销毁**

## 闭包简单理解：
在Javascript语言中，只有函数内部的子函数才能访问该函数的变量，而定义在一个函数内部的函数并且外部能接收到这个函数，那么这个函数就是闭包。(能够读取其他函数内部变量的函数。)
-  闭包是由函数以及创建该函数的词法环境组合而成。这个环境包含了这个闭包创建时所能访问的所有局部变量。


**特点：**
1. 函数套函数，并且外部能访问嵌套函数。
2. 父函数被销毁 的情况下，子函数的`[[scope]]`中仍然保留着父级的变量对象和作用域链，因此可以继续访问父级的变量对象,进而改变父作用域内部的变量值
3. 占用内存，过多使用会产生性能问题，在IE中可能会导致内存泄漏。（可在销毁函数前，将无用的变量删除）

```javascript
// 例一
function A() {
  let a = 1
  B = function ()
  }
}
A()
B() // 1

//例二
function A() {
  let a = 1
 return function () {
      console.log(a)
  }
}
A()()//1

```
### 闭包与内存泄漏
> **内存泄漏是指，页面随着时间的延长使用的内存越来越多而没有及时释放。**

javascript中 不需要开发人员像C语言一样手动使用 `malloc()`分配内存，也不需要用完后使用`free()`回收；而是使用垃圾回收策略来自动管理内存，即找出那些不再使用的值，然后释放所占用的内存。

**垃圾回收只针对局部变量进行回收销毁，全局变量只有网页关闭才会消除。垃圾回收有两种方法引用计数和标记清除**
垃圾回收算法主要依赖于引用的概念
> 什么是引用：在内存管理的环境中，一个对象如果有访问另一个对象的权限（隐式或者显式），叫做一个对象引用另一个对象。

**引用计数**

> 引用计数是跟踪记录每个值被引用的次数。即看一个对象是否有指向它的引用。如果没有其他对象指向它（零引用），说明该对象已经不再需要了。
```javascript
let obj = {
  name:'owen'
}
// 此时 对象 { name:'owen' } 被创建并引用一次
obj = null
// 此时 对象引用次数为零，将被回收机制销毁
```
**引用计数有一个循环引用的问题：如果两个对象互相引用，它们的引用次数永远不会为零，将永远不会被回收，从而占据内存**
```javascript
function obj(){
  let obj1 = {
    name:'owen'
}

// 此时 对象 { name:'owen' } 被创建并引用一次
let obj2 = {}

obj2.name = obj1
obj1.name = obj2
// 此时 两个对象相互引用 ，俩个对象引用次数为二，永远也不会被收回
}
obj()
```

**标记清除**
> 现代浏览器常用的方法，当变量进入环境时（例如，在函数中声明一个变量），这个变量标记为“进入环境”；而当变量离开环境时，则将其标记为“离开环境”。

把"对象是否不再需要"简化定义为"对象是否可以获得"。如把JavaScript想象一个树，每个JS都存在一个根(浏览器中为window对象,Node中为global对象 )，每当一个函数执行，就会生成一个节点。嵌套的函数调用就会有子节点。当函数执行完时，内部的变量都是无法被其他代码访问的，所以它就被标记为“无法被访问”。GC 时，JS 引擎统一对所有这些状态的对象进行回收。
**当进行一轮垃圾回收时，主线程会被阻塞，各个浏览器的时间不同可能是10ms、50ms、1s**

## 应用

### 封装变量
将不需要暴露在全局的变量封装成"私有变量"。

```javascript
// 乘积
let mult = (...args) =>{
        let num = 1;
        for (let val of args ){
          num *= val;
        }
        return num;
}
// 由于每次运行函数都会完全遍历所以形参，效率较低下，我们可以加入缓存机制提供函数性能

let mult1 = (()=>{
 let cache = {};
 return (...args) => {
   if(cache[args.join(',')])  return cache[args.join (',')];
        let num = 1;
        for (let val of args ){
          num *= val;
        }
        return cache[args.join(',')]= num; // 缓存数值
        }
})()

// 我们看到 cache 变量仅仅在 mult 函数中使用，我们可以将它封装在函数内部，减少全局变量，变量发生不必要的错误

```
**如果一个大函数中有些代码块能够独立出来，我们常常把这些代码块封装在独立的小函数里并有个良好的命名，将有助于复用，和注释作用；如果小函数不需要在其他程序中使用，最好使用闭包封装起来**
```javascript
let mult1 = (()=>{
 let cache = {};
 let calculate = (...args)=> {
          let num = 1;
          for (let val of args ){
            num *= val;
          }
        return num
        }
 return (...args) => {
        let property = args.join(',')
        if(cache[property])  return cache[property];
        return cache[property]= calculate.apply(null,args); // 缓存数值
        }
})()
```
### 延续变量
```javascript
// img案例
let imgSrc = (src) => {
  let img = new Image();
  img.src = src;
}
imgSrc('http://xxxx.com/img')
// 在一些低版本浏览器中使用 imgSrc 函数，会丢失一些数据，因为当函数调用结束后 img变量会随之销毁，此时可能未及时发出HTTP请求

// 使用闭包解决数据丢失问题
let imgSrc = (function (){
  let imgs = [];
  return function (src){
  let img = new Image();
  imgs.push(img)
  img.src = src;
}
})()

imgSrc('http://xxxx.com/img')

```
### 三种方法解决循环中 var 定义函数的问题

```javascript
//one 利用闭包
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
//two 设置第三个参数
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer(j) {
    console.log(j)
  }, i * 1000,i)
}

//three  利用 let
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer(i) {
    console.log(i)
  }, i * 1000)
}
```
因为 `setTimeout` 是个异步函数，所以会先把循环全部执行完毕，这时候` i` 就是 固定了，所以会输出一堆 固定值。



## 函数中的this对象
### 普通函数

this 指向取决于 调用它时处在的执行上下文

### 对于new 的方式来说，this 被永远绑定在了赋值变量上面，不会被任何方式改变 this


**箭头函数**
箭头函数中的 this 只取决包裹箭头函数的`第一个普通函数`的 this 否则指向全局。

