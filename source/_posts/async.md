---
title: 异步操作原理(一)
date: 2019-03-11 19:50:34
categories:
- JavaScript
tags: 
- async

---

### 单线程

> - `javascript` 只再一个线程上<label>运行</label>，同时只能执行一个任务，一行一行执行。
> -  javascript 运行在单线程上，并不代表 JavaScript引擎就是单线程的，其实它有多个线程，单个脚本只能在一个线程上运行（主线程），其他线程在后台配合运行。
> - 为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。

这种模式好处在于， 实现起来简单，环境单纯。 但是 耗时较长，后面的任务必须等待前面的任务执行完毕。

### 同步和异步任务
- 同步
未被引擎挂起、在主线程上排队执行的任务。需要前面的任务执行完后才能执行。
- 异步
 1. 被引擎挂起，暂不处理的任务,不进入主线程、而进入任务队列;
 2. <label>只有达到某个条件，异步任务才会进入主线程，不会堵塞异步任务后面的代码 （ajax 是最具代表性的异步任务）</label>


### 异步操作模式

#### 回调函数 `Callback`
> 将函数传递进一个方法中，函数不会立即执行，等待出来结果之后在执行。
> 回调函数是异步操作最基本的方法

容易出现回调地狱（`Callback hell`)
比如多个 ajax 嵌套请求
```javascript
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```
这种方式容易理解和简单，但是不利于<label>维护和阅读</label>；
耦合度高，结构混乱，错误较难追踪，而且每个任务只能指定一个回调。

#### 事件监听
   > 采用事件驱动。
   > W3C规范中定义3个事件阶段：捕获(Netscape)，目标，冒泡（IE）。
   > 事件冒泡：在目标元素上发生click事件的顺序 目标元素 -> 父级元素 -> body -> html -> document
   > 事件捕获： 与冒泡相反，document -> html -> body -> 父级元素 -> 目标元素
   ```javascript
// 原生事件委托
    var parent = document.getElementById('parent');
    parent.addEventListener('click',showColor,false);
        function showColor(e){
            var son = e.target;
            if(son.nodeName.toLowerCase() === 'li'){
                console.log('The color is ' + son.innerHTML);
            }
        }
   //类似 jQuery写法
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
> 消息的发送者（称为发布者） 不会将消息直接发送给特定的接收者（称为订阅者），而是将不消息分为不同的类别，不需要了解哪些订阅者；
> 订阅者,只接收感兴趣的消息，不需要了解哪些发布者
> 可以把事件理解成“信号”,如果存在一个“信号中心”；
 某个任务执行完成，就向信号中心“发布” (`publish`) 一个信号，其他任务可以向信号中心“订阅”(`subscribe`)这个信号，从而知道什么时候自己开始执行

```javascript 

var obj = $({});

obj.on("aaa", function () {
        console.log(111111111111);
    })

obj.on("aaa", function () {
        console.log(222222222222);
    })

$(dom).click(function() {
        obj.trigger("aaa");
    });

```

当 `foo` 执行完毕后，想消息纵向发布 `done`信号，引发执行`fn`

##### 消息过滤
订阅者通常接收信号中心中(消息代理)的一个子集，选择接受和处理的消息过程叫过滤
**过滤形式**
- 基于主题
> 消息被发布到主题或命名通道上；订阅者将受到所有信息，并且所有订阅同一主题的订阅者都将收到同样的信息；发布者赋值定义订阅者所订阅的消息类别
- 基于内容
> 订阅者定义感兴趣的条件，只有当消息的属性或内容满足订阅者的条件，消息才投递到该订阅者。订阅者负责堆消息分类。

##### 拓扑
发布者 发布消息到一个消息代理，订阅者向其注册订阅，由消息代理来过滤

优缺点：
 - 松耦合，发布者和订阅者只需要关注主题内容，相互独立地运行。
 - 扩展性强，通过并行操作，消息缓存，基于树或网路路由等技术，比传统客户端具有更好的扩展性。
- 缺点： 发布者解耦订阅者，问题难以跟踪，无法知道消息传送是成功的还是失败的

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
**注意：**
> HTML标准规定 `setTimeout` 最大间隔 4ms 
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