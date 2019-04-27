---
title: 异步操作原理（二）
date: 2019-03-13 22:19:05
categories:
- JavaScript
tags: 
- async
---

### 事件循环(`eventLoop`) 详解

**并行和并发：**

并行性是指两个或多个事件在同一时刻发生； 
并发性是指两个或多个事件在同一时间段内发生。

#### 任务队列 和 事件循环
> JS 将变量存储在 <label>堆 `heap`</label> 和 <label> 栈 `stack`</label>中
> 堆中存放对象比如 ：数组，对象，函数 
> 栈中存放变量和对象的指针
> 运行时同步任务会排好队，依次执行，而排队的地方就叫执行栈（先进后出）

- JS运行时，除了有一个主线程运行，引擎还提供任务队列 `task queue` , 里面是各种需要当前程序处理的异步任务。（不止一个任务队列）

-  异步任务通常是一个回调函数；如果一个异步任务没有回调函数，是不会放入异步任务队列中，同样也不会进入主线程中执行。

**执行过程：**

1. 主线程会先执行完所有同步任务
2. 同步任务结束，查看任务队列（<label>先进先出的数据结构，排在前面的事件，优先被主线程读取。</label>）里面的异步任务
3. 查看所有满足执行条件的异步任务，放入主线程中变成同步任务，执行对应的回调函数。
4. 执行完毕再将下一个满足执行条件的异步任务放入主线程中执行。
5. 不断重复前三步操作

只要同步任务执行完毕，JS引擎会不停的监测检查任务队列中的异步任务，将满足执行条件且最先进入的任务加入到执行栈中去执行，如此往复。这种检查机制就叫事件循环 `Event Loop`
![event loop](/static/img/eventLoop.png)

#### 微任务与宏任务 
异步任务还会分两层：
- 微任务(`micro task`) 
- 宏任务(`macro task`)

<label>主线程会先查看微任务 `micro task`</label>；
如果没有满足条件的任务，再去宏任务中查找；
如果有则将所有满足条件的微任务按顺序依次加入到主进程中；
执行完毕后再去红任务队列中查找，往复循环。
常见宏任务：
- 定时器 I/O（输入/输出）操作 

微任务(`jobs`)： 
- Promise async/await
#### nodeJS 
 nodeJS 中任务队列 加入了 `process.nextTick` 和 `setImmediate`。
 前者比后者先执行
```javascript
process.nextTick(function A() {
  console.log(1);
  process.nextTick(function B(){console.log(2);});
});

setImmediate(function (){
  setImmediate(function A() {
    console.log(3);
    setImmediate(function B(){console.log(4);});
  });

  setTimeout(function timeout() {
    console.log('TIMEOUT FIRED');
  }, 0);
});
// 1
// 2
// 3
// TIMEOUT FIRED
// 4
//这个结果是不确定的可能是 1234  TIMEOUT FIRED  ||   12  TIMEOUT FIRED 34

```

其二者的调用顺序取决于当前event loop的上下文，如果他们在异步i／o callback之外调用，其执行先后顺序是不确定的。
```javascript
var fs = require('fs')

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout')
  }, 0)
  setImmediate(() => {
    console.log('immediate')
  })
})
//immediate
//timeout

```
这是因为`fs.readFile` callback执行完后，程序设定了`timer` 和 `setImmediate`，因此poll阶段不会被阻塞进而进入check阶段先执行setImmediate，后进入timer阶段执行setTimeout。[setTimeout和setImmediate的差异]( https://juejin.im/post/5aab2d896fb9a028b86dc2fd#comment)

process.nextTick在最新版nodejs中不被推荐使用，推荐使用setImmediate ，原因在于nextTick是在当前帧介绍后立即执行，会阻断I/O并且有最大数量限制；而setImmediate不会阻断I/O，更像是setTimeout(func, 0) 


