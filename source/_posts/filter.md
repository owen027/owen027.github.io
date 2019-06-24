---
title: filter
date: 2019-06-21 14:15:48
categorise:
- css
tags:
- filter
---

# filter
- 将模糊或者颜色偏移等图像效果用于元素，通常用于调整图像，背景和边框的渲染
- css 标准中已内置一些预定义效果的函数，也可通过url使用[SVG滤镜](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/filter)

## 语法
```css
/* URL to SVG filter */
filter: url(filter.svg#id);

/* <filter-function> values */
filter: blur(5px);
filter: brightness(0.4);
filter: contrast(200%);
filter: drop-shadow(16px 16px 20px blue);
filter: grayscale(50%);
filter: hue-rotate(90deg);
filter: invert(75%);
filter: opacity(25%);
filter: saturate(30%);
filter: sepia(60%);

/* Multiple filters */
filter: contrast(175%) brightness(3%);

/* Global values */
filter: inherit;
filter: initial;
filter: unset;
```

## 滤镜属性
- 需要设定某一函数的值。如果该值无效，函数返回“none“。

### url()
- url 函数接收一个XML文件，该文件是一个`SVG filter`，并且包含一个ID值 来指定哪一个滤镜
```css
filter:url(resources.svg#c1) 
```

### blur()
- 定义模糊度
- 可以接受css长度单位，不接受百分比
```css
filter:blur(5px)
```
### brightness()
- 定义亮度
- 默认为1， 1-0 逐渐变暗，1以上逐渐变亮 
- 可以是百分比， 不能为负数

### contrast()
- 定义对比度 
- 1-0 逐渐变成灰黑 1以上颜色逐渐变艳丽
- 可以是百分比， 不能为负数

### drop-shadow()
- 定义阴影效果，合成在图像下面，
- 参数 和 `shadow`, `box-shadow` 相似 
- 和`shadow`, `box-shadow`属性不同之处在于通过filter，一些浏览器为了提升性能会提供硬件加速。
    传参顺序从左至右：
    1. 水平偏移距离正值右偏，负值左偏（offset-x）。<label >必传</label>
    2. 垂直偏移距离正值向下偏，负值上偏（offset-y）。<label >必传</label>
    3. 一个length值(表示距离尺寸的一种css数据格式) 值越大，越模糊阴影会变得更大更淡.）<label >不允许负值</label> 默认为0 （blur-radius）<label >可选</label>
    4. 一个length值，正值会使阴影扩张和变大，负值会是阴影缩小，默认 0
    5. color 色值，在Gecko (Firefox), Presto (Opera)和Trident (Internet Explorer)中， 会应用`color` 属性的值,颜色值省略，WebKit中阴影是透明的。

```css
filter:drop-shadow(16px 16px 10px black)
```

### grayscale()
- 定义灰度
- 取值0 - 1的数值之间，可设置百分比，不可为负数
```css
filter:grayscale(50%);
```

### hue-rotate()
- 定义颜色，色相旋转调整
- 单位 deg
- 可为负值

```css
filter: hue-rotate(90deg)
```

### invert()
- 反向输入图，定义转换比例
- 默认0  0%和100%之间，则是效果的线性乘子。
```css
filter: invert(100%)
```

### opacity()
- 转化图像的透明程度
- 类似于css的`opacity`属性 
- 1-0 逐渐变透明，支持百分比 ，不能为负数
- 和opacity属性不同之处在于通过filter，一些浏览器为了提升性能会提供硬件加速。
```css
filter: opacity(50%)
```

### saturate()
- 定义图像饱和度
- 1-0 至完全不包和，1为正常， 1以上饱和度更高，更艳丽，支持百分比

```css
filter:saturate(369%)
```

### sepia()
- 定义深褐色的色度
- 0 - 1 逐渐变为深褐色 支持百分比，不支持负数

```css
filter: sepia(100%)
```

### 组合函数
- 可以将任意数量的函数一起控制渲染，其中有一个不符合规范将全部失效
```css
filter:sepia(100%) saturate(369%) opacity(50%)
```
## 兼容性
![filter](/static/img/filter.png)

