---
title: React 基础学习
date: 2019-07-23 20:35:20
categories:
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

**当组件的数据完全受到父组件的控制（不再持有 state) 时，将此类组件称之为“受控组件”，并且可简化为==函数组件==**
```javascript
/**
 *因为此组件的数据受到父组件的控制（不再持有 state) 
 *所以将此类组件称之为“受控组件”
 *父组件完全控制了子组件的数据
 * 并且可简化为函数组件
 * @param {*} props
 * @returns
 * 
 */
 function Square(props) {
  return (
    < button className = "square"
             onClick = { props.onClick } >
      { props.value }
    </button>
  );
}

class Board extends React.Component {
  constructor(props){
    super(props)
    this.state = {
      squares: Array(9).fill(null),
      xIsNext:true, // 先落子，并确认该哪位玩家落子
    }
  }
  /**
   * 只接受一个squares副本，而不直接修改本身数据
   * 1. 这样可以简化复杂的功能，不可变性使得复杂的特性更容易实现。
   * 2. 可以跟踪数据的改变，如果直接修改源数据就很难跟踪变化的数据。
   * 3. 可以帮助我们在 React 中创建 purecomponents。可以轻松的确定不可变数据是否发生了改变，
   *    从而确定何时对组件进行重新渲染。
   * @param {*} i
   * @memberof Board
   */
  handleClick(i) {
    const squares = this.state.squares.slice(); 
    squares[i] = this.state.xIsNext? "X":"O";
    this.setState({ squares,xIsNext:!this.state.xIsNext })
  }

  renderSquare(i) { // 返回一个 Square 组件
    return ( < Square
              value = { this.state.squares[i] }// 给子组件传递 value数据
              onClick = {()=> this.handleClick(i)} // 给子组件传递 onClick事件
        />);
  }

 
  render() {
    let {state} = this;
    const status = `Next player: ${state.xIsNext?'X':'O'}`;

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}

```
### React 命名规范
   1. 代表事件监听的 `prop` 命名为 `on[Event]`
   2. 将监听方法命名为 `handle[Event]` 
    
### 为什么创建一个`squares`副本，而不直接修改本身数据

1. 这样可以简化复杂的功能，不可变性使得复杂的特性更容易实现。
2. 可以跟踪数据的改变，如果直接修改源数据就很难跟踪变化的数据。
3. 可以帮助我们在 `React` 中创建 `purecomponents`。可以轻松的确定不可变数据是否发生了改变，从而确定何时对组件进行重新渲染。


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