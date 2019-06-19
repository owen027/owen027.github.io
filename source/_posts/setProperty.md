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

---


# setProperty
- 给一个样式设置新值 `style.setProperty(propertyName, value, priority);`

## 参数
1. propertyName 必须是一个字符串，代表被更改的CSS属性。
  