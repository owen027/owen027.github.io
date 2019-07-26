---
title: react
date: 2019-07-23 20:35:20
categorise:
- React
tags:
- basic
---

# 概览

> `React` 是一个声明式，高效且灵活的用于构建用户界面的 `JavaScript`库。可以将一些简短、独立的代码片段组合成复杂的UI界面，这些片段被称为“组件”。

**React 大体包含下面这些概念：**
- 组件
- JSX
- Virtual DOM
- Data Flow

## 组件
组件，从概念上类似于 `JavaScript` 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的`React 元素`。
**自定义组件命名：必须以大写字母开头，React 会将以小写字母开头的组件视为原生DOM标签。**
###  类组件
通过 `class` 语法来定义组件,并且继承于 `React.Component`,空的 `render()` 方法。
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
React 应用都是构建在组件之上。`ShoppingList`就是一个React 组件类型，`ReactDOM.render` 函数会将组件方到页面上的某个节点元素中。(`render` 返回了一个 `React 元素` ，这是一种对渲染内容的轻量级描述。)
**大多数 `React `应用只会调用一次 `ReactDOM.render()`。**

**其中`props`（是 properties 的简写） 是组件包含的两个核心概念之一，另一个是`state`。**

### `props`
`props`接收一些其他组件的参数（比如上方的 name )，来配置组件，**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改**。

### `state`
`state` 来实现所谓“记忆”的功能。可以通过 `React` 组件的构造函数中设置 `this.state`;`this.state` 应该被视为一个组件的私有属性。
**修改`this.state`值需要通过`this.setState`方法赋值,有些 props 值或 state 值可能是异步更新的，使用对象赋值的方式更改 state 可能无效，可使用回调传参方式更新**
```javascript
this.setState(  (state,props)=> ({count:state.count + props.count}) );
```


### 类组件

**类组件必须包含`render()`，并且return 只能返回一个父元素（类似vue中的template中只能有一个父元素）。**
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
推荐使用箭头函数，避免[this 造成困扰](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)。

### 简单组件(函数组件)
简单组件是一个函数，不用使用`class`关键字，当然就没有`constructor 构造函数和state`
```javascript
const Square =  (props) => {
 return  (<button className= "square"
   onClick= { props.onClick} >{ props.value  }
  </button>)
}
```
### 渲染多个组件
将组件变成数组集合放入花括号中即可渲染多个组件,通常使用数组的`map()`方法
```javascript
const Lis = (props) => {
    const lis = props.list.map((val,key)=> <li key={key}>{key+1}</li>);
    return <ul>{lis}</ul>
}
const list = Array(7).fill(null);
ReactDOM.render(
  <Lis list = {list} />,
  document.getElementById('root')
);
```

### 组件生命周期
```javascript
class Square extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      value:null
    };
  }
   componentDidMount() { // 组件被渲染到 DOM 中后运行
    console.log('DidMount: 1')
  }
  compoentwillUnmount(){ // 组件被删除的时候
    console.log('UnMount: end')

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
## JSX 和事件处理

React 提出的一种叫 JSX 的语法，这应该是最开始接触 React 最不能接受的设定之一,因为前端被“表现和逻辑层分离”这种思想“洗脑”太久了。实际上组件的 HTML 是组成一个组件不可分割的一部分，能够将 HTML 封装起来才是组件的完全体.
大多数的`React`开发者使用 `JSX` 特殊语法，`JSX` 可以更轻松地书写这些结构。

> JSX是一个JavaScript语法扩展。它类似于模板语言，但它具有JavaScript 的全部能力。它最终会被编译为`React.createElement()`函数调用，返回称为 `React元素`的普通JavaScript`对象。

```JavaScript
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


// 上述代码等同于：
/*
return React.createElement("div", {className: "shopping-list"},
React.createElement("h1", null, "Shopping List for ", props.name), React.createElement("ul", null,
                        React.createElement("li", null, "Instagram"),
                        React.createElement("li", null, "WhatsApp"),
                        React.createElement("li", null, "Oculus")
                    )
);

 */

ReactDOM.render(
  <ShoppingList name="Mark" />,
  document.getElementById('root')
);
```
### JSX规范和事件处理
在 JSX 中你可以任意使用JavaScript表达式，只需要用一个**大括号({})**括起来；事实上每个 React 元素都是一个`JavaScript` 对象，可以把它保存在变量中或者作为参数传递。

为避免遇到自动插入分号陷阱，**最好将内容包裹在小括号中，如果只有一行代码则不需要括号**。

```jsx
// 加括号
class App extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// 不用加括号
class App extends React.Component {
  render() {
    return  <li>Instagram</li>;
  }
}
```

**JSX 语法更接近于 JavaScript，所以 ReactDom 使用cameCase(小驼峰命名)来定义属性名称,并且不要使用引号将大括号包裹，两者是不能并存的。对于字符串值使用引号，对于表达式使用大括号**

**`React` 中无法通过 `return false` 的方式阻止默认行为，必须使用`e.preventDefault()`阻止默认事件。但是不用担心`event事件`的兼容问题**

**JSX 本身就能防止[XSS](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击**
```jsx
// 原生DOM
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

// JSX
class App extends React.Component {
  render() {
    return (
        <a href="#" onclick={(e)=>this.handleClick(e)}> //每次渲染时都会创建不同的回调函数。该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。
            Click me
        </a>;
        <a href="#" onclick={(e)=>this.handleClick(id,e)}> // 向事件处理程序传递参数
            Click me
        </a>;
    )
  }
}
```
**事件处理程序回调函数中的 this**
- 在 `JavaScript` 中，`class` 的方法默认不会绑定 `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this`的值为 `undefined`。
``` JSX
class App extends React.Component {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    // 注意: 这是 **实验性** 语法。 使用 Create React App 默认会启用此语法
    handleClick = (e)=> {
        e.preventDefault();
        console.log(e)
    }
  render() {
    return (
        <a href="#" onclick={this.handleClick}>
            Click me
        </a>;
         <a href="#" onclick={this.handleClick。bind(this,id)}> // 向事件处理程序传递参数, 事件对象会被隐式传递
            Click me
        </a>;
    )
  }
}
```
## Virtual DOM

> 当组件状态`state`有更改的时候，`React`会自动调用组件的`render`方法重新渲染整个组件的`UI`。

`React`实现了一个`Virtual DOM`，组件 DOM 结构就是映射到这个`Virtual DOM` 上，`React` 在这个`Virtual DOM` 上实现了一个`diff`算法，**当要重新渲染组件的时候，会通过`diff` 寻找到要变更的DOM 节点，再把这个修改更新到浏览器实际的DOM 节点上**，所以实际上不是真的渲染整个`DOM`树(`React DOM` 只会更新实际改变了的内容)。这个 `Virtual DOM` 是一个纯粹的 JS 数据结构，所以性能会比原生 DOM 快很多。



待续。。。