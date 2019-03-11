---
title: 异步操作原理(一)
date: 2019-03-11 19:50:34
date: 2019-03-05 21:55:38
categories:
- JavaScript
tags: 
- Promise/async

---

### 单线程

> - `javascript` 只再一个线程上<label>运行</label>，同时只能执行一个任务
> -  javascript 运行在单线程上，并不代表 JavaScript引擎就是单线程的，其实它有多个线程，单个脚本只能在一个线程上运行（主线程），其他线程在后台配合运行。
> - 为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。

这种模式好处在于， 实现起来简单，环境单纯。 但是 耗时较长，后面的任务必须等待前面的任务执行完毕。

### 同步和异步