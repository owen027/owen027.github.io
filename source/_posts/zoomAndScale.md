---
title: zoomAndScale
date: 2019-06-17 18:24:26
categories:
- CSS
tags:
- zoom and transform:scale
---
# `zoom` 
- 不是标准属性
- 对文字最小缩到 12pX
- 缩放位置相对于左上角缩放
- 调整时改变了元素占据的空间大小、尺寸，等比例缩放元素
- `*zoom:1` 用来给老版本IE浏览器（IE6/7）清除浮动
- 由于缩小改变原来的空间尺寸，所以整个页面会重新渲染
## 单位
1. 百分比 `zoom:50%` 缩小到原来的一半
2. 数值 `zoom:0.5` 等同 `zoom:50%`，不能为负数
3. `zoom:normal` 等同于 `zoom:1` 

# scale
- 等比例缩放元素  `transform: scale(1)`
- 对文字等比缩放
- 改变时不会从新渲染页面
- 默认居中缩放,原始尺寸不变
- 属于 `w3c` 规范 IE9以上都支持此属性
- 使用 transform 会覆盖以前的 transform 属性，即 以前 transform 对应的属性将被全部替换
- 可控制方向 transform:scale(x,y), transform:scaleX(x)  or transform:scaleY(y)
## 单位 
1. 只能是数值
2. 可以为负数

chrome 中  同时使用两者效果会叠加
---

# transform 变换
- 可以 拉伸，压缩，旋转，偏移目标元素
- 支持 3D 变换

## 类型 
### skew
- 对目标对象进行歪斜变换 `transform: skew()`

#### 参数
- X,Y `transform: skew(X,Y)`  表示沿着 X轴和 Y轴 扭曲角度 默认为0

#### 单位 
1. degress (度)  `transform: skew(100deg,100deg)`
2. grad (百分度)  `transform: skew(10grad,10grad)`
3. rad（弧度）  一个完整的圆 为2π `transform: skew(10rad,10rad)`
4. turn (圈数) 一个完整的圆 为1turn `transform: skew(0.25turn，1.2turn)`
5. 可以为负数
### scale 
- 等比例缩放元素 `transform: scale(1)`
#### 单位 
1. 只能是数值
2. 可以为负数

### rotate