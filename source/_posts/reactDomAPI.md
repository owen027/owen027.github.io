---
title: ReactDOM API
date: 2019-08-02 19:15:12
categories:
- React
tags:
- ReactDOM API
---
## react-dom
- `react-dom` 的 `package` 提供了可在应用顶层使用的DOM方法，所有的顶层 API 都可调用

`React` 支持所有的现代浏览器，IE9及以上版本，但是需要引用相关 [polyfills](https://zh-hans.reactjs.org/docs/javascript-environment-requirements.html),那些在不兼容ES5语法的浏览器中奋斗的同志需要孤军奋战了。


## API

### render()
```javascript
import ReactDOM from 'react-dom'
ReactDOM.render(element,container[, callback])
```
在提供的`container`里渲染一个 `React 元素`，并返回该组件的[引用](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html), 对于 [无状态组件](https://zh-hans.reactjs.org/docs/components-and-props.html#functional-and-class-components)返回 null。
如果 `React 元素` 已经在 `container`中渲染过，那么将会更新里面的元素，并仅会在必要的时候改变DOM 映射新的 React 元素。
callback将在组件被渲染或更新后执行

> 当首次调用时，container节点中所有DOM元素都会被替换，后续的调用则使用 React 的 DOM diff 算法 进行高效更新。
> `rend()`不会修改container节点，之后修改子节点，可以在不覆盖现有子节点的情况下，将组件插入已有的DOM节点中。对服务端渲染容器进行 `hydrate`操作的方式已被废弃，且会在 1
> `rend()`目前会返回对根组件 `ReactComponent` 实例的引用。但应该避免使用它，因为历史遗留下来的内容，在未来版本中，组件渲染某些情况下可能会是异步的。对服务端渲染容器进行 `hydrate`操作的方式已被废弃，且会在 1
> 如果需要根组件 `ReactComponent` 实例的引用，推荐使用[callback ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)。
> 使用`rend()`对服务端渲染容器进行 `hydrate`操作的方式已被废弃，且会在 17版中被 [hydrate()](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate)

### hydrate()

```javascript
ReactDOM.hydrate(element,container[, callback])
```
此方法和 `render()`相同，区别在于 [ReactDOMServer](https://zh-hans.reactjs.org/docs/react-dom-server.html) 渲染的容器中对HTML的内容进行 `hydrate` 操作。React 会尝试在已有的标记上绑定事件监听器。

### unmountComponentAtNode()
```javascript
ReactDOM.unmountComponentAtNode(container)
```
此方法将卸载 DOM 中的组件，会将事件处理器和 state 一并清除。如果指定的 container 中没有对应已挂在的组件，那么此方法什么也不会做，如果组件被移除将会返回 true，如果未移除将返回false

### findDOMNode()
```javascript
ReactDOM.findDOMNode(component)
```
如果组件已被挂载到DOM上，此方法会返回浏览器中x相应的原生DOM，不能用于函数组件，对于在未挂载的组件中调用将发生异常。对于读取DOM中的值很有用，一般**推荐使用 `ref`来获取DOM元素**

**此方法是访问底层DOM节点的应急方案，严格模式中被弃用**


### createPortal()
```javascript
ReactDOM.createPortal(child,container)
```
创建[portal](https://zh-hans.reactjs.org/docs/portals.html)，它提供一种将子节点渲染到DOM节点中的方式，该节点存在于DOM组件的乘此结构之外。

