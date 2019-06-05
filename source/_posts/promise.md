---
title: Pormise
date: 2019-03-14 19:27:09
categories:
- JavaScript
tags: 
- async
---

### Promise

> 为了解决 “回调地狱”（callback hell）而提出的写法
> 允许将 `callback ` 变成链式调用
> 它是一个容器，保存着某个异步操作的结果。

**特点**
- 对象的状态部受外界影响；有三种状态， 进行中( pending ) 、 成功( fulfilled ) 、 失败( rejected );只有异步操作的结果可以决定那种状态。

- 状态一旦改变，就不会更改（resolved 定型），状态的改变只会是两种情况 pengding -> fulfilled or pengding -> rejected;

- <label>  一旦建立就会立即执行 </label>, 中途无法取消，无法得知事件进展，不设置callback，内部将会抛出错误

### 简单实列
> 使用 `Promise` 构造函数生成实例
```javascript

//example
const promise = new Promise(function (resolve,reject) {
    //... do something
    $.get(url,function (data) {
        if (data) {
            resolve(data)
        } 
    }).fail(function(err){
        reject(err)
    })
    
});

promise.then(function(data) {
    console.log(data)
},function(err){
    console.log(err)
})

```
可见 `Promise` 接受一个callback 作为参数， callback有两个参数，都是函数
- 第一个 `resolve` 异步操作成功时调用，将成功的信息作为参数传递出去
- 第二个 `rejcet` 异步操作失败时调用，将失败的信息传递出去

#### then 方法
生成实例后,可以使用 `then` 方法 接收成功状态和失败状态的回调,<label>在当前脚本所有同步任务执行完才会执行</label>
- 第一个参数是成功后的callback (必传)
- 第二个参数是失败后的callback (为可选参数)

```javascript

const promise = (time) => {
    return new Promise((resolve,reject) => {
        console.log("promise")
        setTimeout(resolve,time,'Owen')
    })
}

promise(1000).then((val) => console.log(val)) //"promise"  1s后 "Owen"

//加载图片
function loadImage (url) {
    return new Promise((resolve,reject) => {
        const image = new Image();
        image.onload = () => resolve(image);

        image.onerror = () => reject(new Error(`not lad image at: ${url}`))

        image.src = url;
    })
}

//实现原生ajax(get)

const getJSON = function (url) {
    return new Promise((resolve, reject) => {
        const handler = function () {
            if ( this.readySate !== 4 )  return;

            if (this.status === 200 ) {
                resolve(this.response)
            }else {
                reject( new Error(this.statusText) )
            }
        };

        const client = new XMLHttpRequest();
              client.open("GET",url);
              client.onreadystatechange = handler;
              client.setRequestHeader("Accept", "application/json");
              client.send()

    });
}

getJSON('url').then((res) => {
console.log(res)
},err => console.error(err));


//---


console.log(1)
const p1 = new Promise(function (resolve, reject) {
console.log(2)
 resolve();
});

 console.log(3)
const p2 = new Promise(function (resolve, reject) {
    console.log(4)
  resolve(p1);
})

setTimeout( function () {
    console.log(5)
},0)
window.requestAnimationFrame(function(v){
    // 回调函数执行次数通常是每秒60次
    // 当requestAnimationFrame() 运行在后台标签页或者隐藏的<iframe> 里时，
    // requestAnimationFrame() 会被暂停调用以提升性能和电池寿命。
    //回调函数会被传入DOMHighResTimeStamp参数 （排队的回调开始触发的时间戳）
    console.log(8,v)
    //cancel​Animation​Frame(requestId) 取消 window.requestAnimationFrame
})

p2.then(function(){
    console.log(7)
})
p1.then(function(){
    console.log(6)
})
//p1的状态决定了p2的状态

//1
//2
//3
//4
// 6
// 7
//{<resolved>: undefined}
// 8
// 5


//向另一个 promise 传递 promise 执行状态问题
const p1 = new Promise(function (resolve, reject) {
    setTimeout(() => {reject(new Error('fail')); console.log(1)}, 3000)
})

const p2 = new Promise(function (resolve, reject) {
  setTimeout(() =>{console.log(2); resolve(p1) }, 1000)
})
p2.then(result => console.log(result))
  .catch(error => console.log(error)) //error fail

//传入的 promise 会改变 自己 promise 的状态，变成传入的promise的状态
```
### catch 方法

- 指定发生错误时的回调函数。
```javascript
p.then((val) => console.log('fulfilled:', val))
  .catch((err) => console.log('rejected', err));

// 等同于
p.then((val) => console.log('fulfilled:', val))
  .then(null, (err) => console.log("rejected:", err));



//状态改变后不会再接收之后的状态
const promise = new Promise(function(resolve, reject) {
    resolve('ok');
   reject( new Error('test'));
});

promise
  .then(function(value) { console.log(value) }) //ok
  .catch(function(error) { console.log(error) });

// promise 内部错误不会影响外部的代码，不会退出进程、终止脚本执行
const promise = new Promise(function(resolve, reject) {
    resolve(Owen);
  
});
promise.catch(function(error){console.log(error)}).then(function(v){console.log('Owen:' + v)})
console.log(18)

//18
//error
//Owen:undefined 
 
 //如果将 then 放到第一个将不会执行
 //因为 then 接收的是 promise变量 的状态
 //而放到末尾则接收的是 catch 返回的 Promise 函数的状态

 ```

 ### finally

 不管 promise 的状态怎么变都会执行的方法 （ES2018)
```javascript
//finally方法的回调函数不接受任何参数
promise.finally(() => {
  // 语句
});

// 等同于
promise.then(
  result => {
    // 语句
    return result;
  },
  error => {
    // 语句
    throw error;
  }
);
```

### all

- 用于将多个 Promise 实例，包装成一个新 Promise 实例
- 参数是一个数组或者具有 Interator的接口（返回成员必须是promise)，如果成员不是 Promise 实例，会将其包装成一个porimse 实例
- 等待所以成员的状态都变成 `resolved` 状态， `Promise.all` 才变成 `resolved` 状态，并且<label>成员返回值将以数组形式传递给 `then`</label>
- 数组成员中只有又一位成员为`rejectd` 状态，`Promise.all` 就会变成 `rejected` , 并<label>将第一个变为`rejectd`的成员返回值传递给 `catch`</label>
- 如果成员自己定义了 `then` 或者 `catch`  对应成员的返回值不会传递到 `all` 的回调当中


```javascript
//example
const p1 = new Promise((resolve,reject) => setTimeout(resolve,1000,'Owen'))
            //.then(result =>console.log(result))
           // .catch(err =>console.log(err));

const p2 = new Promise(res => {  throw new Error('报错了');})
            .then(re => console.log(re))
            .catch(err => console.log(err));//error
            
const promise = Promise.all([p1,p2])
            .then(res=>console.log(res)) //1s 后 ["Owen",undefined]  
            .catch(err => console.log(err));
```

### race
- 和 `all` 方法一样  只是成员状态发生改变的情况不同
- 数组中只要有一个成员改变状态， `race` 就改变状态，并将返回值传递给 `race`方法

```javascript
//example
const p1 = new Promise((resolve,reject) => setTimeout(resolve,1000,'Owen'))
            //.then(result =>console.log(result))
           // .catch(err =>console.log(err));

const p2 = new Promise(res => {  throw new Error('报错了');})
            .then(re => console.log(re))
            .catch(err => console.log(err));//error
            
const promise = Promise.race([p1,p2])
            .then(res=>console.log(res)) //undefined 
            .catch(err => console.log(err));

```
### resolve

- 将对象转化为Promise对象
```javascript
Promise.resolve('Owen')
// 等同
new Promise(resolve => resolve('Owen'))
```
传参情况
#### Promise实例
- 返回出入的实例

#### 传入 `thenable`对象
- 具有 `then`方法的对象,将其转化为 `Promise` 对象 

```javascript
var obj = {
    then: function(resolve,reject){
        resolve("Owen")
    }
}

var p = Promise.resolve(obj);
p.then(function(res){
    console.log(res) //"Owen"
})
```
#### 不是对象或者没有 `thenable`方法

- 返回一个新的Promise，状态为 `resolved`
```javascript
const p = Promise.resolve(18);
p.then(function(re){
    console.log(re) //18
})
```

####  reject 
- 返回一个新的Promise，状态为 ` rejectd`
```javascript
const p = Promise.reject('出错了');
// 等同 
const p = new Promise((resolve, reject) => reject('出错了'))


const thenable = {
  then(resolve, reject) {
    reject('出错了');
  }
};

Promise.reject(thenable)
.catch(e => {
  console.log(e === thenable) //true
})
```

### 结合 Generator 使用

```javascript

const g = function* () {
  try {
    const foo = yield getFoo();
    console.log(foo);
  } catch (e) {
    console.log(e);
  }
};

function getFoo () {
  return new Promise(function (resolve, reject){
    resolve('foo');
  });
}


function run (generator) {
  const it = generator();

  function go(result) {
    if (result.done) return result.value;

    return result.value.then(function (value) {
      return go(it.next(value));
    }, function (error) {
      return go(it.throw(error));
    });
  }

  go(it.next());
}

run(g);


```
### [Promise/A+ 规范](https://promisesaplus.com/)
- 实现者为开发者提供开放的 Promise标准，实现可靠，可互操作的JavaScript承诺。

#### 术语（Terminology）
1. `promise` 是一个对象或函数，其`then` 方法 的行为符合此规范
2. `thenable` 定义 `then` 方法的对象或函数
3. `value` 是任何合法的 `JavaScript` 值 （包括 `undefined` 、`thenable` 和 `promise`）
4. `exception` 是使用 `throw` 语句抛出的值
5. `reason` 是一个值（拒因），表明承诺被拒绝的原因（拒绝回调的值）

#### 要求（Requirements）
 
##### Promise states
- Promise 只有三种状态：pending 、 fulfilled（resolved） 和 rejected 状态
1. pending（等待状态）：可以过渡到 resolved 或 rejected 状态
2. fulfilled (执行状态）：无法再改变状态，且只有一个无法改变的`value`
3. rejected （拒绝状态）：无法再改变状态，必须有一个 `reason`
- 这里的 ‘无法再改变’ 意味着不可改变的身份 使用 `===` 判断 `value` 或 `reason`  

##### `then` 方法（The `then` method）
- 一个 `Promise` 必须提供一个 `then` 方法来访问当前 或 最终`value` 或 `reason`
  此方法接收两个参数： `onFulfilled`,`onRejected`  必须忽略其中不是函数是参数

  1. `onFulfilled`：必须在 `promise` 执行状态（`fufilled` ）结束后调用，其第一个参数为 `value`，只能调用一次
  2. `onRejected`： 必须在 `promise` 拒绝状态（`rejected` ）结束后调用, 其第一个参数为 `reason`，只能调用一次

- onFulfilled or onRejected 在[执行上下文](https://es5.github.io/#x10.3) (作用域)堆栈仅包含平台代码之前不得调用
  意味着调用 `onFulfilled`,`onRejected` 须在新一轮 `event loop ` 中执行， 可用 `macro-task`（setTimout,setInterval,I/O,UI rendering, `script`主线程) 或 `micro-task`（Promise, Object.observe,process.nextTick,MutationObserver） 机制来实现
- onFulfilled or onRejected 必须作为函数调用，在 `use strict ` 下 `this 为 undefined `
- `then`在同一个 promise 中可以链式调用，按照对应注册顺序一次回调
- `then`返回一个Promise对象 
  ```javascript
    promise2 = promise1.then(onFulfilled, onRejected);
  ```
   1. 任一 `onFulfilled`或`onRejected`返回一个值x ,则执行 `Promise 解决过程`
   2. `onFulfilled`或`onRejected` 抛出异常 e, 则 `promise2` 必须拒绝执行，并返回 `reason`
   3. 如果`onFulfilled` 不是函数且 Promise1 为执行 `fulfilled `状态，那么  promise2 必须返回和 Promise1  相同的`value`
   4. 如果`onRejected`不是函数且 Promise1 为 `rejected`状态，那么  promise2 必须返回和 Promise1  相同的`reason`

 ###### Promise 解决过程 (The Promise Resolution Procedure)
  - Promise 解决过程是一个抽象的操作，作为输入一个Promise,和一个值，用 `[[Resolve]](Promise2,x)` 表示；
    如果 `x` 有 `then` 方法并且看上去像一个Promise,Promise 解决过程程序会尝试promise采用状态 `x`，否则用 `x` 的 `value`执行Promise;
    对thenables的这种处理使promise的实现进行互操作，只要它们暴露出一个遵循  `Promise/A+`规范 兼容`then`方法即可。它还允许`Promise/A+`规范来“吸收”与合理不符合标准的实现then方法。

 运行 [[Resolve]](promise, x) 需遵循以下步骤：
  1. 如果promise和`x`指向同一个对象，以`TypeError`为理由拒绝执行`promise`。
  2. 如果 `x` 是 promise，则接收其状态：
       2.1 如果 `x` 为 `pending`， 则 Promise 保持等待直至 `x`改变状态
       2.2 如果 `x` 为 `resolved`，则 用相同的 `value` 执行 promise
       2.3 如果 `x` 为 `rejected`，则 用相同的 `reason`执行 promise
  3. 如果 `x` 是 Object or Function:
       3.1 把 `x.then` 赋值给 `then` 
       3.2 如果 `xthen` 抛出异常 结果 `e`，promise 状态变为 `rejected`，`reason` 为 `e`
       3.3 如果 `then`是一个函数，将`x`作为函数作用域 `this` 调用，传递两个回调函数作为参数，第一个为 `resolvePromise`, 第二个参数为 `rejectPromise`
              3.3.1 如果 `resolvePromise` 以值 `y`为参数被调用，以 `r`为 `reason` ，则运行 `[[Resolve]](promise, y)`
              3.3.2 如果 `rejectPromise` 用`r`为参数调用 ，则以 `r`拒绝 promise
              3.3.3 如果 resolvePromise 和 rejectPromise 均被调用，或者被同一参数调用了多次，则优先采用第一次调用并忽略剩下的调用
              3.3.4 如果调用then抛出异常`e`，resolvePromise或rejectPromise已被调用，请忽略它；否则以 `e` 为`reason`(据因)拒绝 promise
       3.4 如果then不是一个函数, 则以`x`为参数执行 promise
   4. 如果 `x `不为Object or Function，以 `x` 为参数执行 promise    

 如果一个 promise 被一个循环的 thenable 链中的对象解决，而`[[Resolve]](promise, thenable)` 的递归性质又使得其被再次调用，根据上述的算法将会陷入无限递归之中。
 算法虽不强制要求，但也鼓励施者检测这样的递归是否存在，若检测到存在则以一个可识别的 `TypeError` 为`reason`(据因)来拒绝 promise  
 ### 手写简版 Promise

```javascript
// 常用变量大写
const PENDING ='pending'
const RESOLVEd = 'resolved'
const REJECTEd = 'rejected'

class myPromise {
  constructor(callback) {
    const that = this
    that.state = PENDING;
    that.resolveCallback = [];
    that.rejectCallback = [];
    
    const resolve = value =>{
      if (that.state === PENDING){
          that.state = RESOLVED;
          that.value = value
          that.resolveCallback.map(cb => cb(that.value))
          }
      }
    const reject = value =>{
        if (that.state === PENDING){
          that.state = REJECTED;
          that.value = value
          that.rejectCallback.map(cb => cb(that.value))
          }
      }

    try {
      callback(resolve,reject)
    }catch(e){
      reject(e)
    }
  }
  then(onResolved,onRejected){
      const that = this

  }
}

  
```