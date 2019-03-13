---
title: 异步操作原理（二）
date: 2019-03-13 22:19:05
categories:
- JavaScript
tags: 
- Promise/async
---

### 事件循环(`eventLoop`) 详解

#### 微任务与宏任务 
异步任务还会分两层：
- 微任务(`micro task`) 
- 宏任务(`macro task`)
<label>主线程会先查看微任务 `micro task`</label>；
如果没有满足条件的任务，再去宏任务中查找；
如果有则将所有满足条件的微任务按顺序依次加入到主进程中；
执行完毕后再去红任务队列中查找，往复循环。

常见宏任务：
 - 定时器 I/O（输入/输出）DOM操作 
 微任务： 
 - Promise
 
 nodeJS 中 任务队列 加入 `process.nextTick` 和 `setImmediate`。
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
```
Node.js文档中称，setImmediate指定的回调函数，总是排在setTimeout前面。实际上，这种情况只发生在递归调用的时候。

process.nextTick在最新版nodejs中不被推荐使用，推荐使用setImmediate ，原因在于nextTick是在当前帧介绍后立即执行，会阻断IO并且有最大数量限制；而setImmediate不会阻断IO，更像是setTimeout(func, 0) [process和setImmediate的差异]( http://blog.nodejs.org/2013/03/11/node-v0-10-0-stable/)

