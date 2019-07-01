---
title: timer
date: 2019-06-28 16:31:41
categories:
- JavasScript
tags:
- timer
---
# 什么是定时器
- 在一段时间间隔后执行一个函数或执行一段代码的方法
- JavaScript 原生提供几种定时器 `setInterval`、`setTimeout` 、`requestAnimationFrame` 
- 执行函数中的作用域未全局作用域this指向全局，可通过 `bind` 方法给执行函数传递参数或指定作用域 (严格模式下，setTimeout( )的回调函数里面的this仍然默认指向window对象， 并不是undefined)

## setInterval
- 每相隔一段时间间隔执行一个函数或执行一段代码的方法
- 通过`clearInterval`方法来取消定时器
```javascript
  let timerInterID =  window.setInterval(callback,delay[,param...])
   setTimeout( clearInterval.bind(null,timerInterID),1000)
```
### 参数 

- **callback** 重复执行的函数或代码段（不推荐使用一段字符串构成的代码，应为这样做不安全，会被不法分子利用）
- **delay** 时间间隔，单位为毫秒（ms）实际间隔可能会稍长（最小间隔是4ms）
- **timerInterID** 每次调用`setInterval`方法返回的唯一 ID，可通过调用 `clearInterval`方法来清除`setInterval`方法
- **param1, ..., paramN** 传递给执行函数（callback）的参数 (<label>IE9 及更早的 IE 浏览器不支持向回调函数传递额外参数。如果你想要在IE中达到同样的功能,你必须使用一种兼容代码</label> )



## setTimeout
- 在一段时间间隔后执行一个函数或执行一段代码的方法
- 通过`clearTimeout`方法来清除定时器
```javascript
let timerTimeID = setTimeout(callback,delay[,param1, ..., paramN ])
```

### 参数

- **callback** 重复执行的函数或代码段（不推荐使用一段字符串构成的代码，应为这样做不安全，会被不法分子利用）
- **delay** 时间间隔，单位为毫秒（ms），默认为0实际间隔可能会稍长（最小间隔为 4ms ）
- **timerTimeID** 每次调用`setTimeout`方法返回的唯一 ID，可通过调用 `clearTimeout`方法来清除`setTimeout`方法
- **param1, ..., paramN** 传递给执行函数（callback）的参数 (<label>IE9 及更早的 IE 浏览器不支持向回调函数传递额外参数。如果你想要在IE中达到同样的功能,你必须使用一种兼容代码</label> )


## requestAnimationFrame

- 类似于`setInterval`方法，执行动画时推荐使用
- 会在浏览器下次重绘前执行函数
- 执行间隔通常是每秒60次，当运行在后台标签页或隐藏在 `iframe`时，会暂停调用
```javascript
let frameID = window.requestAnimationFrame(callback)
setTimeout(cancelAnimationFrame.bind(null,FrameID),1000)
```
### 参数

- **callback** 更新动画帧所调用的函数