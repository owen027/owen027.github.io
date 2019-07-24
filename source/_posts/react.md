---
title: react
date: 2019-07-23 20:35:20
categorise:
- React
tags:
- basic
---

## 概览

> `React` 是一个声明式，高效且灵活的用于构建用户界面的 `JavaScript`库。可以将一些简短、独立的代码片段组合成复杂的UI界面，这些片段被称为“组件”。

**React 大体包含下面这些概念：**
- 组件
- JSX
- Virtual DOM
- Data Flow

## 组件

```javascript
import React from 'react'; // React 的核心库
import ReactDOM from 'react-dom'; // 提供与 DOM 相关的功能
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// <ShoppingList name="Mark" />
ReactDOM.render(
  <ShoppingList name="Mark" />,
  document.getElementById('root')
);

```
React 应用都是构建在组件之上。`ShoppingList`就是一个React 组件类型，`ReactDOM.render` 函数会将组件方到页面上的某个节点元素中。(`render` 返回了一个 `React` 元素，这是一种对渲染内容的轻量级描述。)

**其中`props`（是 properties 的简写） 是组件包含的两个核心概念之一，另一个是`state`。**
`props`接收一些其他组件的参数（比如上方的 name )，来配置组件
**`state` 来实现所谓“记忆”的功能。可以通过 `React` 组件的构造函数中设置 `this.state`。`this.state` 应该被视为一个组件的私有属性**
```javascript
class Square extends React.Component {
  /**
   * @name constructor
   * @param {*} props
   * 每次定义子类的构造函数时，都必须调用 super 方法。
   * 因此，在所有含有构造函数的React组件中，构造函数必须以super(props)开头
   * state 保存着组件中的数据 类似 vue 中的 data 属性
   */
  constructor(props) {
    super(props);
    this.state = {
      value:null
    };
  }
  render() {
    return (
      <button className="square" onClick = {()=>{this.setState({value:'X'})}
      }>
        {this.state.value}
      </button>
    );
  }
}
```
render方法中的`onClick` 事件监听函数中调用`this.setState`方法，可以设置`this.state` 中的属性
推荐使用箭头函数，避免[this 造成困扰](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)
## JSX
> React 提出的一种叫 JSX 的语法，这应该是最开始接触 React 最不能接受的设定之一,因为前端被“表现和逻辑层分离”这种思想“洗脑”太久了。实际上组件的 HTML 是组成一个组件不可分割的一部分，能够将 HTML 封装起来才是组件的完全体
大多数的`React`开发者使用 `JSX` 特殊语法，`JSX` 可以更轻松地书写这些结构。

**上述代码等同于：**
```jsx
return React.createElement("div", {className: "shopping-list"},
React.createElement("h1", null, "Shopping List for ", props.name), React.createElement("ul", null,
                        React.createElement("li", null, "Instagram"),
                        React.createElement("li", null, "WhatsApp"),
                        React.createElement("li", null, "Oculus")
                    )
);
```
在 JSX 中你可以任意使用JavaScript表达式，只需要用一个大括号括起来；事实上每个 React 元素都是一个JavaScript 对象，可以把它保存在变量中或者作为参数传递。

待续。。。