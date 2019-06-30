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
- JavaScript 原生提供几种定时器 `setInterval`、`setTimeout` 、`requestAnimationFrame` 、`setImmediate`

## setInterval
- 在一段时间间隔后重复执行一个函数或执行一段代码的方法

```javascript
  let timerInterID =  window.setInterval(func,delay[,param...])
```
## 参数 

- **func** 重复执行的函数或代码段（不推荐使用一段字符串构成的代码，应为这样做不安全，会被不法分子利用）
- **delay** 时间间隔，单位为毫秒（ms）实际间隔可能会稍长
- **timerInterID** 方法每次执行返回的唯一 ID，可通过调用 `clearInterval`方法来清除`setInterval`方法