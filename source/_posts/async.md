---
title: 异步操作原理(一)
date: 2019-03-11 19:50:34
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

### 同步和异步任务
- 同步
未被引擎挂起、在主线程上排队执行的任务。需要前面的任务执行完后才能执行。
- 异步
 1. 被引擎挂起，暂不处理的任务,不进入主线程、而进入任务队列;
 2. <label>只有达到某个条件，异步任务才会进入主线程，不会堵塞异步任务后面的代码 （ajax 是最具代表性的异步任务）</label>

### 任务队列 和 事件循环
JS运行时，除了有一个主线程运行，引擎还提供任务队列 `taskqueue` , 里面是各种需要当前程序处理的异步任务。（不止一个任务队列）

**执行过程：**
1. 主线程会先执行完所有同步任务
2. 同步任务结束，查看任务队列里面的异步任务
3. 查看所有满足执行条件的异步任务，放入主线程中变成同步任务，执行对应的回调函数。
4. 执行完毕再将下一个满足执行条件的异步任务放入主线程中执行。
5. 任务队列清空，程序结束执行

> 异步任务通常是一个回调函数；如果一个异步任务没有回调函数，是不会放入异步任务队列中，同样也不会进入主线程中执行。

JS引擎会不停的监测检查任务队列中的异步任务，只要同步任务执行完毕，引擎就会起检查那些挂起来的异步任务，这种检查机制就叫事件循环

### 异步操作模式

#### 回调函数
  > 回调函数是异步操作最基本的方法
  ```javascript
  function fn(callback){ 
      //do someing
      callback && callback()
  }
  function foo(){}
        fn(foo)
  ```
  这种方式容易理解和简单，但是不利于<label>维护和阅读</label>,耦合度高，结构混乱，较难追踪，而且每个任务只能指定一个回调

#### 事件监听
   > 采用事件驱动。
   
   类似 jQuery写法
   ```javascript
    fn.on('click',function(){});
    //等同于
    function fn (){
        setTimeout(function(){
            //do someing
            fn.trigger('done')
        },1000)
    }
   ```
   这种方法比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，可以去“耦合”(`decoupling`),便于实现模块化。 但是整个程序都会变成事件驱动，流程不清晰。

#### 发布/订阅

 > 可以把事件理解成“信号”,如果存在一个“信号中心”；
 某个任务执行完成，就向信号中心“发布” (`publish`) 一个信号，其他任务可以向信号中心“订阅”(`subscribe`)这个信号，从而知道什么时候自己开始执行

```javascript 
//订阅
jQuery.subscribe("done",fn);
// 发布
function foo(){
    setTimeout(function(){
        //do someing
        jQuery.publish('done')
    },1000)
}
//取消订阅
jQuery.unsubscribe('done',fn)
```

当 `foo` 执行完毕后，想消息纵向发布 `done`信号，引发执行`fn`

### 异步操作的流程控制（多个异步操作如何确定异步操作的执行顺序，如何保证这种顺序执行）
 
#### 串行执行

 > 一个任务执行完毕后，再执行另一个
 ```javascript
 let arr = [1,2,3,4,5];
 let results = [];
const async = (arg,callback)=>{
      console.log(`参数：${arg}，1秒后返回`);
      setTimeout(()=>callback(arr*2)},1000);
  } 
const final = res => console.log(`完成：`,res);
const series = item =>{
    if(item){
        async(item,result=>{
            results.push(result);
            return serise(items.shift();)
        })
    }else{
        return final(results[results.length-1]);
    }
}
series(items.shift());
```
上面代码 `series` 就是一个串行函数； 类似与同步任务

#### 并行执行

> 所有异步任务同时执行，全部执行完毕，再执行 最终 (`final`) 函数

```javascript
 let arr = [1,2,3,4,5];
 let results = [];
const async = (arg,callback)=>{
      console.log(`参数：${arg}，1秒后返回`);
      setTimeout(()=>callback(arr*2)},1000);
  } 
const final = res => console.log(`完成：`,res);

 for (let item of items){
     async(item,(result) =>{
         results.push(result)
     })
     if(results.length == items.length){
         final(results[results.length-1]);
     }
 }
 ```
 上面代码，for 循环会同时执行5异步任务，等他们执行完毕再执行 `final` 函数。 过个并行任务较多，容易耗尽系统资源，拖慢运行

 #### 并串结合
 > 限制并行执行任务的数量，避免占用过多系统资源

```javascript
let items = [1,2,3,4,5];
let results = [];
let running = 0,limit = 2;
const async = (arg,callback)=>{
      console.log(`参数：${arg}，1秒后返回`);
      setTimeout(()=>callback(arr*2)},1000);
  } 
const final = res => console.log(`完成：`,res);

const launcher = () => {
    while (running < limit && items.length>0){
        let item = items.shift()；
        async (item,(res)=>{
            results.push(result);
            running--;
            items.length? launcher(): final(results);
        })
        running++
    }
}
```
上面代码，最多只能运行两个异步任务，当前 `running` 记录运行的任务数量，低于门槛 limit 就会新增一个任务，直到任务执行完毕。

###  轮询
```javascript
let hash = window.location.hash;
const hashWatcher = () =>{
    if(window.location.hash != hash){
        updatePage()
    }
}

setInterval(hashWatcher,1000)
```
`setInterval` 时间 间隔是" 开始执行 " 之间的间隔,不会考虑每次执行的任务时间，所以两次执行间隔会小于指定时间。比如 指定 100ms  任务本身消耗 105 ms，那么第一次执行完毕后，第二次会立即执行。

如果要固定间隔，可以使用 `setTimeout`
```javascript
let hash = window.location.hash;
const hashWatcher = () =>{
    if(window.location.hash != hash){
        updatePage()
    }
    setTimeout(hashWatcher,1000)
}
setTimeout(hashWatcher,1000)
```
 由于定时器每执行一次,会返回一个整数，连续执行，返回值比上一次大1
 ```javascript
 (function() {
  // 每轮事件循环检查一次
  var gid = setInterval(clearAllTimeouts, 1000);

  function clearAllTimeouts() {

    var id = setTimeout(function() {
            console.log("g:"+gid) 
    },0);
   while (id > 0) {
    if (id !== gid) {
        clearTimeout(id);
      }
      id--;
    }

  }
})();

 ```
###  防抖（`debounce`）
> 在某些场景下不希望事件执行太频繁，我们可以设置一个阀门临界值，再一段时间内只执行一次,或者一段时间过后执完所有任务

```javascript
//一段时间只执行一次

let perform = (fn,time) => {
  let  flag = true;
    return  function () {
        let self = this,arg = arguments;
         if(flag){
             flag = false;
             fn.apply(self,arg);
               setTimeout(() => { 
                flag = true
             },time)
         }
    }
}
let fn = () => console.log(1);
setInterval(perform(fn,2000),500);
 
 // 经过一段时间再执行所有要执行的任务
 let debounce = (fn,delay) =>{
    let timer = null;
    return function (){
        let self = this,arg = arguments;
         timer && clearTimeout(timer);
         timer = setTimeout(() => {
             fn.apply(self,arg)
         },delay)
    }
 }
setInterval(perform(fn,2500),500);
```

-------
[参考原文链接： http://wangdoc.com](http://wangdoc.com/javascript/async/general.html#%E4%BB%BB%E5%8A%A1%E9%98%9F%E5%88%97%E5%92%8C%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF)