---
title: React.Component API
date: 2019-07-29 22:42:29
categories:
- React
tags:
- React API
---

## React.Component
> 使用ES6 classes 方式定义React组件的基类
**最好不要建立自己的组件基类，因为，在React组件中，代码重用的主要方式是组合而不是继承**

### setState(updater[, callback])
> 用于更新用户界面以响应事件处理器和处理服务器数据的主要方法

此方法不会总数立即更新组件，他会批量推迟更新，如果需要使用`setState` 后立即读取 `state`可能会是旧值。
使用 `componentDidMount` 方法或使用 `setState`的回调函数可解决旧值问题。
注意 **shouldComponentUpdate 方法返回false，将不会重新渲染页面**

#### 参数一 updater函数 或者对象
> 带有连个形参的函数 或者一个对象类型
```javascript
// updater function
this.setState((state,props)=>({name:state.name+props.name}))

// object type
this.setState({name:this.state.name})
```
updater 函数中 `state` 是对应组件 `state` 的引用，它不应直接被修改；须返回一个对象，会与state进行浅合并

object 类型中 `setState` 也是异步的，并在**同一周期**内对多个 `setState` 进行处理,如果后续状态取决于当前状态，建议使用 updater 函数的形式代替。
**example：**
```javascript
Object.assign(
  previousState,
  {count: state.count + 1},
  {count: state.count + 1},
  ...
)

```
[深入学习：何时以及为什么 setState() 会批量执行？](https://stackoverflow.com/questions/48563650/does-react-keep-the-order-for-state-updates/48610973#48610973)
[深入：为什么不直接更新 this.state？](https://github.com/facebook/react/issues/11527#issuecomment-360199710)


`setState`的第二个参数为可选的回调函数，它将在`setState` 完成合并并重新渲染组件后执行，通常建议使用`componentDidMount`方法代替。

### forceUpdate(callback)
> 默认情况下，组件数据发生变化就会重新渲染，如果 render 方法依赖其他数据，可以使用 `forceUpdate` 强制让组件刷新

**此方法会跳过 `shouldComponentUpdate`方法。但是子组件会正常触发。通常应该避免使用forceUpdate方法，尽量在render方法中使用 state 和 props。**

### Class属性

#### defaultProps
此属性可以给对应的组件添加 props,通常用于 prop未赋值且不能为null。
**example：**
```javascript
class Example extends React.Component {
 render(){
   return (
          <div>
            <h3>{this.props.name}</h3>
            <main>
            <Text text ='null'/> // props.text 将是 null
            </main>
          </div>
        )
 }
}
Example.defaultProps = {
  name:'Owen',
  text:'Owen is a programmer '
}
```

### 实例属性

#### props 类似 vue中的 props
`this.props` 包括被该组件调用者定义的 props。其中`this.props.children` 是一个特殊的 prop，通常由 JSX 表达式中的子组件组成，而非组件本身定义。

#### state 类似 vue 中的data
组件中 state 包含随时可能发生变化的数据。由用户自定义，是一个普通的对象，**永远不要直接改变 this.state，因为后续调用的 setState() 可能会替换掉你的改变。请把 this.state 看作是不可变的。**


## React.PureComponent
> React.PureComponent 与 React.Component 相似二者区别于 `shouldComponentUpdate`的实现
- React.Component 并未实现 `shouldComponentUpdate`方法
- React.PureComponent 中以层对比 prop 和state 的方式实现`shouldComponentUpdate`方法

在某些情况下使用`React.PureComponent`可提高性能
**注意：如果对象中包含赋值的数据结构，可能因无法监测深层次的差别，产生错误的对比结果。所以仅在props和state较为简单时使用`React.PureComponent`。此外，`shouldComponentUpdate`方法会跳过所有子组件树的prop 更新**

## React.memo
> `React.memo` 是高级函数组件，它与 React.pureComponent 相似，但不支持class组件。此方法仅做为[性能优化](https://zh-hans.reactjs.org/docs/optimizing-performance.html)的方式存在
```javascript
const MyMemo = (props) =>{
  return <li>/* ... */</li>
}
const MemoComponent = React.memo(MyMemo)
```
如果函数组件在给定相同的 props 情况下渲染结果相同，就可将其保证在 `React.memo`中调用，这样就可通过记忆组件渲染结果的方式来提高组件的性能，意味着React 将跳过渲染组件的操作并直接复用最近一次渲染的结果。

**默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。**
```javascript
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
export default React.memo(MyComponent, areEqual);
```

## [参考资料](https://zh-hans.reactjs.org/docs/react-component.html#setstate)