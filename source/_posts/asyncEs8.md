---
title: Async/await
date: 2019-03-14 19:27:09
categories:
- ES6
tags: 
- async
---

## ES2017引入async 函数
- Generator 的语法糖

```javascript

//generator
const fs = require('fs');

const readFile = function (path) {
    return new Promise(function (resolve,reject) {
        fs.readFile(path, function(err,data) {
            if (err) return reject(err);
            resolve(data);
        });
    });
};

const g = function* () {
    const f1 = yield readFile(url1);
    const f2 = yield readFile(url2);
}

// async
const asyncFile = async function () {
    const f1 = await readFile(url1);
    const f2 = await readFile(url2);

}

/**
 * 将 g 函数的 * 变成 axync
 * 将 yield  变成 await 
 */

```

### async  对  generator 进行以下优化

- 不再需要调用 `next` 方法就能执行
- 更加语义化
- 实用性更强 `await` 后面可以是 Promise , 原始类型值（会自动转成立即 resolved 的 Promise 对象）
- 返回值为Promise 对象更加方便，<label>当函数执行遇到 `await` 时暂停执行，等到异步操作完成，再执行后面的代码</label>
- async 函数 return 的返回值会成为 `then` 方法的参数
- async 函数内部抛出错误 将会被 `catch`方法接收
```javascript
// example
function timeout(ms) {
    return new Promise(resolve => setTimeout(resolve,ms))
}
async function asyncPrint(val, ms ) {
     await timeout(ms);
    console.log(val)
}

asyncPrint('Owen',1000) //1s 后 "Owen"

//因为 asnyc 返回 Promise 所以可以改用 async

async function timout(ms) {
    await new Promise(resolve => setTimeout(resolve,ms));
}

async function asyncPrint(val, ms ) {
     await timeout(ms);
    console.log(val)
}

asyncPrint('Owen',1000) //1s 后 "Owen"

/* 定义 async 函数 */

//声明
async function f(){}

//表达式
const fn = async function () {};

// 对象
const obj = {
    async f(){}
}

// class 
class Storage {
   async getAvatar(name) {  }
}

//箭头函数
const foo = async () => {};

/* then方法接收return 的参数 */
async function foo (){
    return "Owen"
}
foo().then(re =>console.log(re)); //Owen

/* 接收错误对象 */
async function fn (){
    throw new Error(18)
}
fn().catch(er =>console.log(er)) //error

```
- asyn回调只有等所有 `await` 后面的Promise 执行完毕或者报错和`return`才会执行。
```javascript
async function fn() {
    var time = + new Date()
    console.log(time,1)
   let p1 =  await new Promise(resolve => setTimeout(resolve,1000));
   console.log(+ new Date() - time ,2)
   let p2 =  await new Promise(resolve => setTimeout(resolve,2000));
   return +new Date() - time;
}
fn().then(time => console.log(time,3));
//  1s 1
//  2s 2
//  3s 3
```
### await
- await 后面是 `thenable` 对象，会和返回 promise 对象 一样返回结果
```javascript

class Sleep {
  constructor(timeout) {
    this.timeout = timeout;
  }
  then(resolve, reject) {
    const startTime = Date.now();
    setTimeout(
      () => resolve(Date.now() - startTime),
      this.timeout
    );
  }
}

(async () => {
  const actualTime = await new Sleep(1000);
  console.log(actualTime);
})();
```
- 如果 `await` 后面的Promise 状态为 `rejected` 那么结果将会传递给 `catch` 方法；并且函数会中断执行,除非使用 `try..catch` 或者 对应的 `Pomise` 使用 `catch`方法 。
```javascript
async function ay () {
    
    await Promise.reject(18).catch((err) => {
        console.log(err)
        })
    return "Owem"
}
ay().then(res =>console.log(res))
//18
//Owem
```
- 多个 `await` 后面的异步，如果不存在继发关系，最好同时触发

```javascript 
async function fn() {
    var time = + new Date()
    console.log(time,1)

   let [p1,p2] = await Promise.all([ 
       new Promise(resolve => setTimeout(resolve,1000)),
       new Promise(resolve => setTimeout(resolve,2000))]);

       return +new Date() - time;
}
fn().then(time => console.log(time,2));
```
- 只能再 async 函数中使用 await 
```javascript
async function ay () {
    
    function (){
        await 1
    }
    return "Owem"
}
ay()  //error

```

### async 原理
- 将Generator 函数和执行器包装再一个函数中

```javascript
function autoactuator (gen) {
 return new Promise((resolve,reject) => {
     const g = gen();
    let step = (next) => {
        let n;
        try{
            n = next();
        }catch(err) {
            return reject(err)
        }
        if(n.done){
            return resolve(n.value)
        }
        Promise.resolve(next.value).ten((v) =>{
            step(() => g.next(v));
        },function(e){
            setp(() => g.throw(e));
        });
    }

    step(() => g.next(undefined));
 })
}
```
