---
title: CSS中的 zoom 和 scale 区别
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
- 支持 3D 变换
- 可以 拉伸，压缩，旋转，偏移目标元素
- 只能转换由盒子模型定位的元素。根据经验，如果元素具有display: block，则由盒模型定位元素。（转换行内元素，须设置display样式属性）
- 支持 IE10以上 IE9 需加私有前缀 -ms-（IE） -webkit-(chrome，safari) -o-（opera）-moz- （Firefox)
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
- 旋转元素 `transform: rotate(x,y)`
- 默认以目标对象中心为圆点转动
#### 单位
同 `skew`

### translate
- 平移目标元素 `transform: translate(x,y)`

#### 单位
同 `skew`

---
# transition
- 平滑的改变CSS的值
- 只要CSS 属性值发生改变都会实现过渡效果
- 支持IE10 以上
## 类型

### transition-property
- 指定过度属性 `transition-property:height`   height 就会参与过渡

### transition-duration
- 指定持续时间  `transition-duration:2s`  指过渡持续 2S

#### 单位
- s (秒) ms(毫秒)
### transition-delay
- 过渡开始作用之前需要等待的时间。 `transition-delay:0.2s` 等待0.2S 后执行过渡
#### 单位
- s (秒) ms(毫秒)
### transition-timing-function
- 指定动画运动时的节奏 `transition-timing-function:ease` 匀速运行

### 参数
- ease
- ease-in 由慢到快
- ease-out 由快到慢
- ease-in-out 由慢到快在到慢
- linear 线性过度
- step-start
- step-end
- steps(5,end)
- [其它参数设置](https://developer.mozilla.org/en-US/docs/Web/CSS/timing-function)

## transition 简写
-  transition: property-color duration timing-function; `transition: height 0.3s ease;`

---
# animations
- 用来指定一组或多组动画，每组之间用逗号相隔
- 支持IE10以上
## 类型

### -webkit-animation-name;
- 指定应用的一系列动画，每个名称代表一个由@keyframes定义的动画序列

#### 值
- none 默认静止
- 定义 @keyframes 后的 名字
- 由大小写不敏感的字母a-z、数字0-9、下划线(_)和/或横线(-)组成。第一个非横线字符必须是字母，数字不能在字母前面，不允许两个横线出现在开始位置。
##### 举个栗子
```css
.cylon_eye {
    -webkit-animation-name: resize;
}
@-webkit-keyframes resize { from { margin-left: -20%; } to { margin-left: 100%; }  }
        @keyframes resize { from { margin-left: -20%; } to { margin-left: 100%; }  }
```
### -webkit-animation-duration
- 指定一个动画周期的时长
- 默认为 0 无动画

#### 单位
- s , ms `-webkit-animation-duration: 1.5s;`

###  -webkit-animation-iteration-count
- 定义动画运行的次数 `-webkit-animation-iteration-count:4`

#### 属性
- 默认运行一次
- infinite 无限循环
- 播放的次数 不可为负值. 可以用小数定义循环(0.5 将播放动画到关键帧的一半

### -webkit-animation-direction
- 指示动画是否反向播放 ` -webkit-animation-direction: alternate;`

#### 属性
- normal 默认 每个动画循环结束，动画重置到起点重新开始
- alternate  动画交替反向运行，反向运行时，动画按步后退，同时，带时间功能的函数也反向 计数取决于开始时是奇数迭代还是偶数迭代
- reverse 反向运行动画，每周期结束动画由尾到头运行。
- alternate-reverse 动画第一次运行时是反向的，然后下一次是正向，后面依次循环。决定奇数次或偶数次的计数从1开始。
    -webkit-animation-timing-function: ease-in-out;
### animation-timing-function
- 每一个动画周期中执行的节奏，作用于一个关键帧周期而非整个动画周期
#### 值
同 `transition-timing-function`

## 简写语法
- animation-name，animation-duration, animation-timing-function，animation-delay，animation-iteration-count，animation-direction，animation-fill-mode 和 animation-play-state 属性的一个简写属性形式
```css
.cylon_eye {
  background-color: red;
  background-image: linear-gradient(to right,
      rgba(0, 0, 0, .9) 25%,
      rgba(0, 0, 0, .1) 50%,
      rgba(0, 0, 0, .9) 75%);
  color: white;
  height: 100%;
  width: 20%;
/* 调用动画 指定对应 持续时间 运动时的节奏 延迟时间 循环次数 对应动画 */
  -webkit-animation: 4s linear 0s infinite alternate move_eye;
          animation: 4s linear 0s infinite alternate move_eye;
}
/* 定义动画 */
@-webkit-keyframes move_eye { from { margin-left: -20%; } to { margin-left: 100%; }  }
        @keyframes move_eye { from { margin-left: -20%; } to { margin-left: 100%; }  }
```