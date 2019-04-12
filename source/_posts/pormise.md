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

//实现原生ajax

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
```
