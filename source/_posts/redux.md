---
title: Redux 学习
date: 2019-08-04 12:22:58
categories:
- React
tags:
- Redux
---

## 概念
> Redux 是一个可以预测的JavaScript 状态控制器，专注于状态管理，有store,state, action,reducer 等API（类似 vue 中的 vux）

Redux 可编写构建行为状态一致的应用，可运行在客户端、服务的 和 原生应用 环境中，并且容易测试。 最重要的是，它提供很好的开发体验，编译后的代码可通过devtools实时预览调试。

Redux 可以和 React 一起使用，也可以和其他视图库（如 [Vue](https://cn.vuejs.org/v2/guide/installation.html)）使用。它很小（2K大小 包含依赖 ），并且有一个庞大的插件生态系统。



## 安装
使用 npm
```bash
npm install redux -S
```
使用 yarn

```bash
yarn add redux -P
```

### 使用案例

```javascript
// 引入 redux
import {createStore} from 'redux'

const ADD_NUM = 1;
const SUB_NUM = 2


// 创建 redecer
const counter = (state = 6,action)=>{
    switch(action.type){
        case ADD_NUM:
            return ++state
        case SUB_NUM:
            return --state
        default
        return state
    }

}
// 创建 store
const store = createStore(counter)
// 获取state
const num = store.getState()
// 发布信息更改state
store.dispatch({type:1})

// 订阅 获取state
store.subscrible(lister)  // 没执行一次dispatch （改变state) 就执行一次 lister 方法
// 设置每次state变化 后的行为
function lister(){
 console.log(store.getState())
}
store.dispatch({type:0})

```

## 核心概念
> 更新state中的数据，须通过action（就是一个普通对象） 来更改,这样可清晰的知道应用中发生了什么，数据为什么变动，怎样变动。而核心想法是如何根据这些 action 对象来更新 state。
```javascript
{type:1}
{type:2}
```

将state和action结合成函数，这就是reducer。它只接受 state和action 两个参数，并返回新的state函数。

```javascript
 console.log(store.getState())// 6
}
```
当然一个应用不单单只有一个reducer,我们可以通过redux来将它们合并。

## Redux 三大原则


### 单一数据源 （Single source of truth）
> 整个应用程序的 state 存储在一个单一 store 的对象树（object tree）中

这样更容易创建创建通用应用，来自服务端的 state 可以在无需编写更多代码的情况下被序列化并注入到客户端中。这样单一的 state 树更容易调试和检查。它还能在开发过程中保持应用程序的状态，从而加快开发周期。一些在传统上难以实现的功能（撤销/重做），将其state 存储在树中，从而变得更加简单。
```javascript
store.dispatch({
  type: 1
})

store.dispatch({
  type: 2
})
```
### State 是只读的 （State is read-only）
> 改变state的唯一方法是触发 action, action是描述发生什么事件的对象

这样确保视图和网络请求不会直接修改state，反而，它们明确表达想要修改的意图。因为所有的变化都被集中处理，且按照严格的顺序一个接一个执行。所以没有细微的竞争条件需要注意。 Action是一个普通对象，所以可以对它们进行打印、序列化、存储，然后后期调试和测试。
```javascript
store.dispatch({
  type: 1
})

store.dispatch({
  type: 2
})
```

### 使用纯函数进行更改
> 要如何通过 action改变指定的state tree ,需要编写reducres 函数

Reducer 只是一些纯函数，他接收 state 和 action 两个参数，并且返回新的state ,
```javascript
import { combineReducers, createStore } from 'redux'
// 创建 redecer
const counter = (state = 6,action)=>{
    switch(action.type){
        case ADD_NUM:
            return ++state
        case SUB_NUM:
            return --state
        default
        return state
    }

}
const counter1 = (state = 6,action)=>{
    switch(action.type){
        case ADD_NUM:
            return ++state
        case SUB_NUM:
            return --state
        default
        return state
    }

}
const reducers = combineReducers({ counter, counter1 }) // 通过 combineReducers 合并reducer
const store = createStore(reducers)
```

## 基础教程

### Action
> Actions 是将数据从应用程序发送到 store 的有效负载。它们是store 的唯一数据来源，通过 `store.dispatch()` 传递给 store
example:

```javascript
const ADD_TODO = 'ADD_TODO'

{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```
Actions 本质上是普通对象。并且该对象必须有一个`type` 属性来表示将要执行的动作，通常 `type` 被定义成字符串常量。当规模变大时，可以存放到单独的文件种管理。
```javascript
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```
> 注意：使用单独的模块或文件定义 常量`type`不是必须的，对于小应用来说，使用字符串`type`方便。而在大型项目种定义常量利大于弊。

出了`type`属性外其他属性可自行定义，对于规范参考[Flux Standard Action](https://github.com/redux-utilities/flux-standard-action)