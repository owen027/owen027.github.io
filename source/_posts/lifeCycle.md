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
1. constructor()
2. static getDerivedStateFromProps()
3. render()
4. componentDIdMount()

### 当组件的 `props` 或 `state` 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
1. static getDerivedStateFromProps()
2. shouldComponentUpdate()
3. render()
4. getSnapshotBeforeUpdate()
5. componentDidUpdate()
   
### 当组件从 `DOM` 中移除时会调用如下方法：
- componentWillUnmount()

### 当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
- static getDerivedStateFromError()
- componentDidCatch()
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