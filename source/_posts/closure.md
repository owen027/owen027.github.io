---
title: closure
date: 2019-06-27 14:15:07
categories:
- JavaScript
tags:
- closure
---


# 什么是闭包（closure）？
在理解闭包前，须理解变量作用域。作用域分全局和局部作用域，是指变量有效访问的范围。
变量无权访问子作用域，只能访问自己和父级以上的作用域

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

