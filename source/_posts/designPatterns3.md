---
title: 高阶函数
date: 2019-07-12 12:32:40
tags:
---
# 什么是高阶函数
> 函数可以作为参数被传递，也可以作为返回值输出

## 函数作为参数传递
> 将函数作为参数传递，我们就可以抽离以部分容易变化的业务逻辑，这样可以分离业务代码中变与不变的部分

**回调函数：**
> 将函数传进一个方法中，函数不会立即执行，等待出来结果之后在执行。
```javascript
let func = function (callback){
    if(n === 'owen'){
        callback() //回调函数
    }
}
 function say (){
     console.log('Hello Word')
 }
 func(say)
```
**Array 对象常用的方法**

```javascript
[1,2,3,4].forEach(iteration)
 function iteration(v){
     console.log(v)
 }
 ```
## 作为返回值输出
> 让函数继续返回一个可执行的函数，意味着运行过程是可延续的。

**判断数据类型**
```javascript
let type = type =>{
    return obj => Object.prototype.toString.call(obj) === `[object ${type}]`
}
let isArray = type('Array'),isString = type('String'),isNumber = type('Number'),isObject = type('Object');

// or
let Type = (function(){
    let type = {},types = ['Object','Array','Number','String']
    for (let val of  types) {
        (function(str){
            type[`is${str}`] = (obj) => Object.prototype.toString.call( obj ) === `[object ${str}]`
        }(val))
    }
    console.log(type)
    return type
}())
Type.isNumber(2) // true
```
**实现AOP（面向切片编程）**
>AOP 通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。
JAVA 语言中 AOP 将一些跟核心业务逻辑模块无关的功能抽离出来，通常包括日志统计、安全控制、异常处理灯。再通过“动态织入”的方式掺入业务逻辑中。
**好处：** 可以保持业务逻辑模块的纯净和高内聚，方便复用日志统计等功能模块。

JavaScript中实现AOP是指把一个函数“动态织入”到另一个函数之中
**具体实现：**
```javascript
Function.prototype.before = function(beforeFn){
    let that = this; // 谁调用指向谁 下面是由 func 函数调用所以是指向 func

    return function( ...args){
        beforeFn.apply(this,args) // 执行回调函数 beforeFn
        return that.apply(this,args) // 执行原函数
    }
}

Function.prototype.after = function(afterFn){
    let that = this; // 谁调用指向谁 下面是由befor函数调用所以是指向 befor
    return function( ...args){
        let ret = that.apply(this,args) // 执行并接收原对象
        afterFn.apply(this,args) //  执行回调函数 beforeFn
        return ret
    }
}
var func = function (){
    console.log(2)
}
func = func.before(function (){
    console.log(1)
}).after(function (){
    console.log(3)
})
func()
// 1 2 3
```
**函数柯里化 （function currying）**
> 在数学和计算机科学中，柯里化是将多个参数的函数转换成一系列使用一个参数的函数，且返回接受余下的参数的新函数
