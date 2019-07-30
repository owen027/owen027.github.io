---
title: React 生命周期函数
date: 2019-07-29 23:39:45
categories:
- React
tags:
- life cycle
---

## 组件生命周期函数

### 当组件实例被创建并插入 `DOM` 中时，其生命周期调用顺序如下：
####  constructor(props)
> 如果不需要初始化 state 或 不进行方法绑定，则不需要使用该方法
在组件挂载之前会先调用该方法，在实现构造函数时必须先调用`super(props)`方法，否则会出现BUG
**通常，构造函数仅用于两种情况：1. 初始化 `state` 2. 为事件处理函数绑定实例**
**在该方法中不要使用 `setState()` 方法,在其他方法中使用`setState()`改变 state**
[为什么 props 复制给 state 会产生 bug](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)
```javascript
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = {
    counter: 0,
    name:props.name // 严禁这样赋值，props.name值更新时 state.name并不会更新
   };
  this.handleClick = this.handleClick.bind(this);
}
```

#### static getDerivedStateFromProps() （此方法不常用）
> 此方法会在 `render` 方法之前调用，并且初始化和数据更新时都会调用，它返回一个对象更新 state，如果返回null 则不更新任何内容。

此方法适用于 state 值在任何时候都取决于props 的情况。


#### render()
> render 是 class 组件必须实现的方法
当该方法被调用时，它会监测 props 和 state 的变化，并且返回以下类型之一：
- `React 元素`：通过JSX创建，渲染成对应的DOM节点或自定义组件
- **数组或fragments：** 使render方法可以返回多个元素 [frgments](https://zh-hans.reactjs.org/docs/fragments.html)
- `Portals`：可以渲染子节点到不同的DOM子树汇中[portals](https://zh-hans.reactjs.org/docs/portals.html)
- **字符串或数值类型：** 在DOM中会被渲染为文本节点、
- `Boolean 或 null`：什么都不渲染
**render函数最好为纯函数，即在不修改组件 `state`情况下，每次调用时都返回相同的结果，并且不会直接与浏览器交互**
> 如果要和浏览器交互，可以在其他生命周期函数中执行，**注意：`shoouldComponentUpdate`方法中返回false,将不会调用render方法**
```javascript
class Example extemds React.Component{
shouldComponentUpdate(nextProps, nextState){
  return false
}
render(){ // 不会执行
  <div>owen</div>
  }
}
```


#### componentDIdMount()
> 此方法会在组件挂载后（插入DOM树中）调用，初始化的数据的好地方





### 当组件的 `props` 或 `state` 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

#### static getDerivedStateFromProps() （此方法不常用）(已解释)

#### shouldComponentUpdate() （此方法不常用）
> 当state 或 props 变化时该方法会在渲染执行前调用默认返回值为true,首次加载不会被调用
**根据该方法的返回值判断组件输出是否受当前 state 或 props 更改的影响。默认为 state 每次更新重新渲染**

此方法进仅做为性能优化的方式存在，不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。你应该考虑使用内置的 PureComponent 组件，而不是手动编写 shouldComponentUpdate()。PureComponent 会对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。



#### render()(已解释)

#### getSnapshotBeforeUpdate() （此方法不常用）
> 此方法在最近一次渲染输出（提交到DOM节点）之前调用。使组件能在发送更改前从DOM中捕获一些信息（如 位置）。此生命周期的返回值将作为参数传递给 `componentDidUpdate()`
```javascript
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动​​位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}

```
上述示例中，重点是从 getSnapshotBeforeUpdate 读取 scrollHeight 属性，因为 “render” 阶段生命周期（如 render）和 “commit” 阶段生命周期（如 getSnapshotBeforeUpdate 和 componentDidUpdate）之间可能存在延迟。

#### componentDidUpdate(prevProps, prevState, snapshot)
> 此方法会在数据更新后立即调用，首次加载不会被调用,在此方法中使用 **`setState`必须将它放到条件语句中**，否则会导致死循环。还会导致额外的重新渲染，影响性能
```javascript
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

**注意：如果 shouldComponentUpdate() 返回值为 false，则不会调用 componentDidUpdate()。**



### 当组件从 `DOM` 中移除时会调用如下方法：

#### componentWillUnmount()
> 此方法会在组件卸载销毁前调用，可以执行必要的清理操作，如 定时器，取消网络请求，或清除componentDidMount() 中创建的订阅等。



### 当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

#### static getDerivedStateFromError() （此方法不常用）

#### componentDidCatch() （此方法不常用）



## Example
```javascript
class Square extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      value:null
    };
  }
  static getDerivedStateFromProps(props, state) {
    // 实例化组件之后以及在重新呈现组件之前调用新的静态生命周期。它可以返回要更新的对象state，或null指示新对象props不需要任何state更新。
  }
   componentDidMount() { // 组件被渲染到 DOM 中后运行
    console.log('DidMount: 1')
  }

  shouldComponentUpdate(){
    // 更新前
  }

  getSnapshotBeforeUpdate(){
    //
  }
  componentDidUpdate() {
    // 更新后
  }

  static getDerivedStateFromError() {
      // 出错时
  }
  componentDidCatch(){
    // capture error
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