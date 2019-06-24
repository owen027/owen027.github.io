---
title: setProperty
date: 2019-06-18 16:31:50
categories:
- CSS
tags:
- setProperty
---
# css3 原生 var 变量
- 可以降低维护成本，有更高性能，文件更高压缩率。


## 语法
1. 声明 `--*  `
    - `*` 表示变量名称，对于命名不能包含特殊字符（ $，[，^，(，%  ）等等
    -  必须在块级（`{}`）区域内声明,且只能给属性名的值，设值，不能给属性设置
```css
:root {
     --color: purple; 
     }

```
2. 使用
- 通过`var(x,y)`函数调用
- 函数的可选第二个参数用作回退值。如果第一个参数引用的自定义属性无效，则该函数将使用第二个值。
```css
div {
    color: var(--color);
}

```

## CSS变量不合法的缺省特性
- CSS变量，只要语法是正确的，就会作为正常的声明解析
- 如果发现变量值是不合法的，就让默认值代替
```css
body {
  --color: 20px;
  background-color: #fff;
  background-color: var(--color, #000);
}
/* 等同于 */
body {
  background-color: transparen;
}
```
## CSS变量不合法的空格尾随特性
- 变量值后面会尾随空格,所以变量值最好加单位
```css
body {
  --width: 20;
 width: var(--width)px;
}
/* 等同于 */
body {
    /* 宽度失效 */
 width: 20 px; 
}
```

## CSS变量的相互传递特性
- 声明CSS变量时候可以引入其他变量
```css
body {
  --width: 20px;
  --width: var(--width);
  --height: calc( var(--width) / 10);
}
```
## 兼容性
![var](/static/img/var.png)


---


# setProperty
- 给一个样式设置新值 `dom.style.setProperty(propertyName, value, priority);`

## 参数
1. propertyName 必须是一个字符串，代表被更改的CSS属性。
2. `value` 对应 `propertyName` css 属性值。如果未传，则为空字符串
3. priority 设置css 的权重 "important" ,"undefined", ""
4. 如果不设置权重 则可简写为 `dom.style.propertyName = "value"`

---

# getPropertyPriority
- 获取一个样式属性的权重
- `dom.getPropertyPriority('property')`
- 返回值 'important' 或  ''

---
# removeProperty
- 删除属性 `dom.getPropertyPriority('property')`
- 返回被删除的属性值
- 多个单词组成的属性要用连字符连接各个单词，不接收驼峰命名法的形式。

---

# getComputedStyle
- 兼容 IE9及以上
- 获取当前元素所以最终使用的 CSS属性值
- 返回一个实时的 `CSSStyleDeclaration` 对象，当元素的样式更改时，它会自动更新本身。

## 语法
- `let styles = window.getComputedStyle(element, [pseudoElt]);`
- `element`: Dom 元素
- `[pseudoElt]`: 可选指定一个伪元素 （在Gecko2.0 (Firefox 4 / Thunderbird 3.3 / SeaMonkey 2.1)之前版本，参数pseudoElt是必要的。如果为null，则不指定其他主要浏览器必须指定此参数。Gecko已经更改为匹配其他浏览器的行为。）

## `getComputedStyle` 和 `style` 区别
- `element.style` 同样可以获取 `css` 对象，可读写，而`getComputedStyle(el)` 只能读
- `element.style`返回已设置的样式属性,而 `getComputedStyle(el)` 会将所有属性给返回
`getComputedStyle(el)`
![CSSStyleDeclaration](/static/img/CSSStyleDeclaration.png)
`element.style`
![el.style](/static/img/elStyle.png)


## defaultView 
- 此属性只读。
- 在浏览器中，`document.defaultView` 返回window与文档关联的对象，或者null如果没有可用的对象。
- `getComputedStyle` 不仅挂载在 window对象上，而且还挂载在 `defaultView`对象上
- 因为在firefox3.6上访问（iframe)必须 `defaultView`对象上访问


## css属性解析值（resolved value），计算值（computed value） 和 应用值（used value）
- 属于 `getComputedStyle`方法返回的值，大多数属性是一个计算值，有些是应用值

### 计算值（computed value）
- 子元素继承父元素的值，通过指定值计算出来
- 处理特殊的值 `inherit`(元素获取其父元素的计算值),`initial `(属性的初始默认值),`unset`(优先用 `inherit` 的样式，其次会应该用`initial`的样式)
- 进行计算，达到对应属性值的要求
- 计算值所需要的计算通常包括将相对值（em,rem %,)转换成绝对值（px）


### 应用值（used value）
- 完成所有计算后最终使用的值

计算出CSS属性的最终值有三个步骤
1. 指定值（Specified value） 从样式表中接收（按权重规则来接收）未设置将使用继承值或者默认值
2. 按规范算出计算值（span 指定 position: absolute 后display 变为 block） 
3. 计算布局（尺寸比如 auto 或 百分数 换算为像素值） 最后就是应用值， 只能使用 `getComputedStyle`来获取最终的应用值

