---
title: React API
date: 2019-07-31 17:44:31
categories:
- React
tags:
- React API
---
## createElement()
> 创基并返回指定类型的 [React元素](https://zh-hans.reactjs.org/docs/rendering-elements.html)。其中类型参数既可以是DOM标签字符串，也可以是[React组件类型](https://zh-hans.reactjs.org/docs/components-and-props.html),或是[React fragment](https://zh-hans.reactjs.org/docs/react-api.html#reactfragment)类型

```javascript
React.createElement(type,[props],[...children])
```
 JSX 编写的代码将会被转换成使用 React.createElement() 的形式。



## cloneElement()
> 以 element 元素为样板克隆并返回新的React元素，返回元素的props将新的props与原始的prosp浅合并。新的子元素取代现有的子元素，原始元素的 key 和 ref 将被保留

通过 ref 获取子节点时，将不会意外地从祖先节点上窃取它。相同的 ref 将添加到克隆后的新元素中。
```javascript
React.cloneElement(element,[props],[...children])
```

## isValidElement()
> 验证对象是否为React元素，返回值为 true or false

## React.children
> 提供用于处理 `this.props.children` 不透明数据结构的方用方法。

### React.Children.map
- 在每个直接子节点上调用一个函数，并将 `this`(执行期上下文) 设置为 `thisArg`。
- 如果`Children` 是一个数值，它将被遍历并为数组中的每个子节点调用该函数。
- 如果子节点为`null` 或 `undefined`,则返回对应的 null 或 undefined。

```javascript

React.Children.map(children,function[(thisArg)])

```
**如果children 是一个Frgment对象，它将被视为单一子节点的情况处理，不被遍历**

### React.Children.forEach
> 和 `React.Children.map` 类似，但是不会返回数组
```javascript

React.Children.forEach(children,function[(thisArg)] )

```
### React.Children.count
> 返回 children 中组件总数
```javascript

React.Children.count(children)

```

### React.Children.only
> 验证children是否只有一个React元素，有则返回它，无则抛出错误

```javascript

React.Children.only(children)

```
**此方法不接受`React.Children.map`的返回值，因为它是一个数组

### React.Children.toArray
> 将children 这个复杂的数据结构以数组的形式扁平展开并返回，且为每个子节点分配一个key.想要在渲染函数中操作子节点的集合时，非常实用
```javascript

React.Children.toArray(children)

```
**注意：此方法在拉平展开节点列表时，更改key值以保留嵌套数组的语言。即 toArray 会为返回数组中的每个key添加前缀，使每个元素key的范围都限定在此函数入参数组的对象内**

## React.Fragment
> `React.Fragment` 能在不额外创建DOM元素情况下，让 render方法返回多个元素

```javascript

render(){
    return (
        <React.Fragment>
          <ChildA />
          <ChildB />
          <ChildC />
        </React.Fragment>
    )
}
// 简写
render(){
    return (
        <>
          <ChildA />
          <ChildB />
          <ChildC />
        </>
    )
}

```
[更多信息](https://zh-hans.reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)


## React.createRef
>创建一个能通过 ref 属性附加到 React元素的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)

```javascript
class Example extends React.Componet {
    constructor(props){
        this.inputRef = React.createRef();
    }
    reder(){
        return <input type="text" ref= {this.inputRef} />；
    }

    componentDidMount(){
        this.inputRef.current.focus()
    }
}
```

## React.forwardRef
> `React.forwardRef` 会创建一个React组件，能将其接受的`ref` 属性转发到其组件树下的另一个组件中。
- [转发refs到DOM组件](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
- [在高阶组件中转发 refs](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

**`React.forwardRef`接受渲染函数作为参数。使用props和ref z作为参数调用此函数，返回 React节点**

```javascript
const Example = React.forwardRef((props,ref)=> ( <button ref={ref} className="FancyButton">
    {props.children}
  </button>));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```


## React.lazy
> React.lazy()允许你定义一个动态加载组件。有助于减少 bundle的体积，并提高首屏加载效率

待续。。。