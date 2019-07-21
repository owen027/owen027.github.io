---
title: 高阶函数（Heigher-order function）
date: 2019-07-12 12:32:40
caterogies:
- design patterns
tags:
---
# 什么是高阶函数
>《javascript设计模式和开发实践》中定义 函数既可作为参数被传递，也可以作为返回值输出

**满足以下条件:**

- 接受一个或多个函数作为输入
- 输出一个函数

高阶函数一般是那些函数型包含多于函数。在函数式编程中，返回另一个函数的高阶函数被称为Curry化的函数。
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
### 实现AOP（面向切片编程）
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
### 函数柯里化 （function currying）
> 在数学和计算机科学中，柯里化是将多个参数的函数转换成一系列使用一个参数的函数，且返回接受余下的参数的新函数

**curring 又称部分求值；一个 curring 函数首先会接收一些参数，该函数并不会立即求值，而是继续返回另外一个函数，而刚传入的参数会被保存在形成的闭包中，待函数真正需要求值的时候，之前的所以参数都会被一次性用于求值**

**简单示例：**
```javascript
function add(a,b) {
    return a + b
}
add(1,2) // 3

// 如果柯里化
add(1)(2) // 3

```

接下来使用 currying 实现一个几天之内消费总和的函数
```javascript
// 普通方法
var cost = (function() {
    var args = [];
    return function(){
        if(!arguments.length){
            let money = 0
            for (let val of args ){
                money += val;
            }
            return money
        }else{
            [].push.apply(args,arguments)
        }
    }
})()
cost(100);
cost(100);
cost(100);
cost(); // 300

cost(100)(100)(100)

// currying
/**
 * 保存原函数参数返回到新函数中使用
 */

//  func(100,100,100) //300
function count (...args){
    let num = 0;
     if(args.length>1){
         for (let v of args){
             num +=v
         }
         return num
     }else{
         return args[0]
     }
}

var  curry = function(func){
        let args = []
    return function fn(...Args){
        if (Args.length){
            [].push.apply(args,Args)
            return fn
        }else{
            return func.apply(this,args)
        }
    }
}
cost = curry(count);

cost(100);
cost(100);
cost(100);
cost(); // 300


```


### 函数节流

JavaScript 中大多数情况都是用户主动出发函数，除非函数本身的实现不合理，否则一般不会遇到跟性能相关的问题，少数情况下，函数不是由用户直接触发控制，可能被频繁调用造成严重的性能问题。
比如：
```javascript
window.addEventListener('resize', function(e) {
   // do something...
});
window.addEventListener('scroll', function(e) {
   // do something...
});
Dom.addEventListener('mousemove', function(e) {
   // do something...
});

// progress
xhr.upload.addEventListener("progress", function(result) {
    // do something...
}, false);

// ...
```
**上述事件1秒种触发很多次，并且常常操作DOM节点，非常损耗性能，浏览器会因此吃不消而卡顿；实际我们不需要触发如此高的频率因此我们可以在一段时间内忽略掉一些执行次数**

#### 节流原理：
> 如果持续触发事件，可每隔一段时间只执行一次。
 
##### 使用定时器实现节流

> 将即将被执行的函数用 `setTimeout` 函数延迟一段时间执行，如果该定时器未执行完成则忽略接下下来的需被执行的函数。
```javascript
 function throttle(func,wait) {
      let timer, firstFlag = true; //第一次立即执行
      return function(...args) {
          if(timer)  return false; // 如果存在定时器这不执行

          let that = this;
          if(firstFlag){
              firstFlag = false;
             return func.apply(that,args);
          }
          timer = setTimeout(function(){
               clearTimeout(timer);
               timer = null;
               func.apply(that,args);
            },wait)
      }
 }
 window.addEventListener('scroll', throttle(function(e) {
  console.log(e) 
},1000));
```

### 函数防抖

> 和节流一定时间段内只调用一次事件处理函数不同，防抖是一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。（用户不再触发对应事件才执行一次事件）
```javascript
function debounce(func,wait) {
    let timer;
    return function(...args) {
        let that = this;
        clearTimeout(timer);
        timer = setTimeout(function(){
            func.apply(that,args)
        },wait)
    }
}
 window.addEventListener('scroll', debounce(function(e) {
  console.log(e) 
},1000));
```