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
> yield 后面的表达式以分号作为结束语句
> 一种可以暂停函数执行的表达式，配合 `next`方法使用。
> 只能再在 `Generator` 函数中使用，普通函数中使用会报错.
> `yield` 如果用<label>在另一个表达式中,必须用圆括号包起来</label>，作为函数参数或者赋值表达式右边可以不用加括号。

`next` 运行逻辑

- 调用 `next` 遇到 `yield`表达式 暂停执行函数后面的操作，并且 <label> 紧跟 `yield` 后面的表达式的值</label>作为返回对象 `value` 属性的值。
- 再次调用 `next`,继续执行`yield`表达式 后面的逻辑代码，直到下一个 `yield`表达式 或者 `return` 语句，返回值为 对象 `value` 属性的值。
- 如果没有  `return` 语句 ，则  `value` 属性的值为 `undefined`。
- <label>只有调用`next`方法，才会执行对应 `yield` 后面的表达式</label>
```javascript

function* g(){
    yield 1 + 2
}
let num = g() 
num //g {<suspended>}
num.next() //{value:3,done:false}
num.next() //{value:undefined,done:true}
```
### 使用 Interator
> 由于 `Generator` 函数是遍历器生成函数，因此 把它赋值给对象的 `[Symbol.interator]`属性，该对象就可以被遍历
```javascript
let iter = {};

iter[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
}

[...iter] //[1, 2, 3]
```

### next 参数

> `yield` 本身的返回值为 undefined， 而 `next`方法可以带一个参数，当作<label>上一个 `yield`的返回值<label>

```javascript
let g = fn();

 g.next() //{value:0,done:false}
 g.next() //{value:1,done:false}
 g.next() //{value:2,done:false}
 g.next(1) //{value:0,done:false}

 function* fn (){
     for (let  i = 0; true; i++){
         let reset = yield i;
         if(reset) i = -1;
     }
 }

function* dataConsumer() {
  console.log('Started');
  console.log(`1. ${yield}`);
  console.log(`2. ${yield}`);
  return 'result';
}

let genObj = dataConsumer();
genObj.next();
// Started
//{value: "undefined", done: fales}
genObj.next('a')
//1. a
//{value: "undefined", done: fales}
genObj.next('b')
//2. b
//{value: "result", done: true}

```
###  for...of 中使用 Generator
> 可以不用调用 `next` 方法
> <label> 如果 返回对象 done属性值为 true， 循环终并且不包含该对象返回值；
```javascript
function* fn(){
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let i of fn()){
    console.log(i);
    //1 2 3 4 5 undefined
}


//斐波那契数列

function* fibonacci() {
  let  [prev, curr] = [0, 1];
  for (;;) {
      yield curr;
      [prev,curr] = [curr, prev + curr];
  }
}

for (let f of fibonacci()) {
    if (f > 1000) break;
    console.log(f)
}

//遍历任意对象方法

function* objectEntries(obj) {
    let propKeys = Reflect.ownKeys(obj);

    for (let key of propKeys){
         yield [key,obj[key]];
    }
}

let person = {name:'Owen', age:18};

for (let [key,value] of Object.entries(person)){
    console.log(key,value);
    //name Owen
    // age 18
}

for (let [key,value] of objectEntries(person)){
    console.log(key,value);
     //name Owen
    // age 18
}
//或者

person[Symbol.interator] = objectEntries

for (let [key,value] of person){
    console.log(key,value);
     //name Owen
    // age 18
}

```

### Generator throw
> 在函数体外抛出错误，再在 函数体内捕获。
> throw方法抛出的错误要被内部捕获，前提是必须至少执行过一次next方法。因为只有执行一次 `next`方法，函数才开始执行。
> 
```javascript
  function* g() {
       try {  yield;  } catch (e) {
          console.log('内部1',e )
      };

     try { yield; } catch (e) {
          console.log('内部2',e)
      }; 
  }
  
  let  t = g();
  t.next(); //{value: undefined, done: false}
  try{
      t.throw('a');
      t.throw('b');
      t.throw('c');
  } catch(e){
      console.log('外部',e)
  }

  //内部1 a
  //内部2 b
  //外部 c

```

### Generator return
> 结束 Generator 函数 并且给定对象返回值
> 如果有 `try...finally`，且在执行中，return方法会等待`finally` 执行完毕，再执行。
```javascript
function* g(){
    yield 1;
    yield 2;
}

let ge = g();

ge.next(); // { value: 1, done: false }
ge.return('Owen'); // { value:"Owen", done: true }
ge.next() // { value: undefined, done: true }

// try...finally
function* numbers () {
  yield 1;

  try {
    yield 2;
    yield 3;
  } finally {
    yield 4;
    yield 5;
  }

  yield 6;
}
var nun = numbers();
nun.next() // { value: 1, done: false }
nun.next() // { value: 2, done: false }
nun.return(7) // { value: 4, done: false }
nun.next() // { value: 5, done: false }
nun.next() // { value: 7, done: true }
```
**next()、throw()、return()**
> 都是让 Generator 恢复执行，并且使用不同语句替换 `yield`  

### yield*
> Generator 函数默认不在内部调用另一个 Generator 函数 是没有效果的，如果<label>放到 `yield` 后面 会返回一个遍历器对象</label>
> `yield*` 后面的 `Generator` 函数（没有return语句时），等同于在 `Generator` 函数内部，部署一个 `for...of` 循环。
> 如果 `
```javascript
//默认
function* f(){
    yield 1;
    yield 2;
}

function* g(){
    yield 'x';
    f();
    yield 'y';
}

for(let key of  g()){
    console.log(key)
    //"x" "y"
}

//yield*

function* y(){
    yield 'x';
    yield* f();
    yield 'y';
}

for (let k of y()) {
    console.log(k);
    // "x" "y" 1 2
}

//无 return
function* concat(iter1, iter2) {
  yield* iter1;
  yield* iter2;
}

// 等同于

function* concat(iter1, iter2) {
  for (var value of iter1) {
    yield value;
  }
  for (var value of iter2) {
    yield value;
  }
}
```
