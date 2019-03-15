---
title: symbol
date: 2019-03-15 19:19:04

categories: 
- ES6
tags:
- Symbol
---

## 含义
> ES6 引入的原始数据类型，表示独一无二的值
> 通过 `Symbol`函数生成的原始值，不是对象，所以无法添加属性和方法
```javascript 
let s = Symbol();
let s1 = new Symbol('s'); //TypeError
// 不能通过 new Symbol 否则会报错

s.toString()//"Symbol()"

let s2 = Symbol();
s == s2 // false
//独一无二的特性

Boolean(s) //true
!s // false

s + s1 //error 
s + 1  //error 
s + "1" // error 
//symbol 不能参与任何值的运算

```
对象中的 symbol 无法通过 `.` 点运算访问
```javascript
let sm = Symbol()
const s ={
    [sm]: 1
};
s.sm //undefined
s[sm]//1

```


### 消除魔术字符串
> 代码中多次出现，与代码形成强耦合的某一个具体的字符串或者数值。
```javascript

function getDate(num,options){
    let data = 1;
    switch (num){
        case 1:
        data = num + 1;
        break
        case 2 :
        data = num * 2;
        break
    }
    return data
}
// case 右边的 1， 2 就是魔术数值
//一般把魔术字符串 变成变量来消除

let obj ={
    one:Symbol(),
    two:Symbol()
}
function getDate(num,options){
    let data = 1;
    switch (num){
        case obj.one:
        data = options + 2;
        break
        case obj.two :
        data =  options * 2;
        break
    }
    return data
}
getDate(obj.one,3)

```
### 作为对象属性名

- `for...in`、`for...of`，`Object.keys()` , `Object.getOwnPropertyNames()`,`JSON.stringify()`返回。
- 不是私有属性，可以通过 `Object.getOwnPropertySymbols()`获取指定symbol名
```javascript
let a = Symbol('a');
let b = Symbol('b');
const obj = {
    a:1,
   [a]:'hello',
   [b]:'word'
}

console.log(Object.getOwnPropertySymbols(obj)) //(2) [Symbol(a), Symbol(b)]
```
-可以通过 ` Reflect.ownKeys() ` 返回所有类型的属性名
```javascript
let a = Symbol('a');
let b = Symbol('b');
const obj = {
    a:1,
   [a]:'hello',
   [b]:'word'
}
Reflect.ownKeys(obj) // ["a", Symbol(a), Symbol(b)]
```
###  `Symbol.for()`与`Symbol()`

> Symbol('a') 和 Symbol.for():两种方法都会生成新的 `Symbol` 值
> Symbol('a') 使用10次，会生成10个新的 Symbol
> Symbol.for('a')，使用10次，会生成一个 Symbol 并创建一个key值
> Symbol.keyFor(Symbol.for('a')),会返回一个全局 key 值，可以在不同的iframe 或 service worker 获取同一个key

```javascript
let s = Symbol('a');
let s1 = Symbol.for('s');
let s2 = Symbol.for('s');
s == s1 //false
s1 == s2 //true

let key = Symbol.keyFor(s2) //s
```

 