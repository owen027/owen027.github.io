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
**此方法不接受`React.Children.map`的返回值