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