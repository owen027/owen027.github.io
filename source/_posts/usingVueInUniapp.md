---
title: uniapp中使用vue的问题
date: 2019-12-26 16:06:05
categories:
- notes
tags:
- Notes using Vue in uniapp
---

**uniapp完全支持vue的生命钩子函数、模板语法、计算属性、条件渲染、列表渲染**

## 组件

表单控件使用原生或uniapp生态库中的[组件](https://ext.dcloud.net.cn/)
非H5不支持v-html [跨端的富文本处理方案详情](https://ask.dcloud.net.cn/article/35772)
非H5端不支持列表：
- Slot（scoped 暂时还没做支持）
- 动态组件
- 异步组件
- inline-template
- X-Templates
- keep-alive
- transition （可使用 animation 或 CSS 动画替代）
- 老的非自定义组件编译模式不支持在组件引用时，在组件上定义 click 等原生事件、v-show（可用 v-if 代替）和 class style 等样式属性(例：<card - class="class-name"> </card> 样式是不会生效的)。建议更新为自定义组件模式
- [老的非自定义组件编译模式](https://ask.dcloud.net.cn/article/35843)组件里使用 slot 嵌套的其他组件时不支持 v-for。建议更新为自定义组件模式
**uni-app只支持vue单文件组件（.vue 组件）**render，和<script type="text/x-template"> 字符串模版等 非H5端都不支持

## 各个平台使用v-for的差异
- 在H5中，`v-for="(item, index) in 10"` item 是从 1开始，其它平台从0开始
- 非H5中，不支持第三个参数 `v-for="(value, name, index) in object"` index 是不支持的



## Class和Style

### class支持的语法
```html
<view :class="{ active: isActive }">111</view>
<view class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }">222</view>
<view class="static" :class="[activeClass, errorClass]">333</view>
<view class="static" v-bind:class="[isActive ? activeClass : '', errorClass]">444</view>
<view class="static" v-bind:class="[{ active: isActive }, errorClass]">555</view>

```

### style支持的语法
**以 :style=""方式设置的px单位是不会被编译器转换**
```html
<view v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">666</view>
<view v-bind:style="[{ color: activeColor, fontSize: fontSize + 'px' }]">777</view>
```

**非H5端不支持 `classObject` 和`styleObject`语法，同时不支持在自定义组件上使用class和style的绑定**
```html
<!-- 不支持的写法如下 -->
<template>
    <view :class="[activeClass]" :style="[baseStyles,overridingStyles]"></view>
</template>

<script>
    export default {
        data() {
            return {
                activeClass: {
                    'active': true,
                    'text-danger': false
                },
                baseStyles: {
                    color: 'green',
                    fontSize: '30px'
                },
                overridingStyles: {
                    'font-weight': 'bold'
                }
            }
        }
    }
</script>
```
### 事件处理器
vue与uniapp对应的事件名称：

```js
// 事件映射表，左侧为 WEB 事件，右侧为 ``uni-app`` 对应事件
{
    click: 'tap',
    touchstart: 'touchstart',
    touchmove: 'touchmove',
    touchcancel: 'touchcancel',
    touchend: 'touchend',
    tap: 'tap',
    longtap: 'longtap',
    input: 'input',
    change: 'change',
    submit: 'submit',
    blur: 'blur',
    focus: 'focus',
    reset: 'reset',
    confirm: 'confirm',
    columnchange: 'columnchange',
    linechange: 'linechange',
    error: 'error',
    scrolltoupper: 'scrolltoupper',
    scrolltolower: 'scrolltolower',
    scroll: 'scroll'
}
```
#### 事件修饰符
- `.stop`各平台全支持
- `.prevent`仅H5
- `.self`仅H5
- `.once`仅H5
- `.capture`仅H5
- `.passive`仅H5

**禁止蒙版下的页面滚动可使用 @touchmove.stop.prevent="handle"**

无按键修饰符

