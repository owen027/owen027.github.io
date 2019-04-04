---
title: Generator
date: 2019-04-04 19:28:27
categories: 
- ES6
tags:
- Generator
---

##  Generator

>  ES6 提供的一种异步编程解决方案
> Generator 函数是一个状态机，封装了多个内部状态。还是一个遍历器对象生成函数。返回<label>遍历器对象</label>，可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数特征
- `function `关键字和函数名之间 有 `*`  ES6 没有规定，function关键字与函数名之间的星号，写在哪个位置。
-  函数体内部有 `yield` 表达式
```javascript

function* generator() {
    yield  'Owen';
    yield 18;
    return 'end';
}

//调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象 Iterator 
let person = generator();

person.next() //{value:'Owen',done:false}
person.next() //{value:18,done:false}
person.next() //{value:'end',done:true}
person.next() //{value:undefined,done:true}
person.next() //{value:undefined,done:true}

```

### yield

> 一种可以暂停函数执行的表达式，配合 `next`方法使用。

`next` 运行逻辑

- 调用 `next` 遇到 `yield`表达式 暂停执行函数后面的操作，并且 <label> 紧跟 `yield` 后面的表达式的值</label>作为返回对象 `value` 属性的值。
- 再次调用 `next`,继续执行`yield`表达式 后面的逻辑代码，直到下一个 `yield`表达式 或者 `return` 语句，返回值为 对象 `value` 属性的值。
- 如果没有  `return` 语句 ，则  `value` 属性的值为 `undefined`。

