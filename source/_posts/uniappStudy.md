---
title: uniapp 学习
date: 2019-12-24 10:33:23
categorise:
- notes
tags:
- uniapp
---

## 概叙

`uni-app`是基于vueJS框架开发的微端应用框架，可一套代码跨ios\Android\H5\小程序等多个平台运行

可通过官方开发的[HBuilderX](https://www.dcloud.io/hbuilderx.html) IDE 创建`uni-app`项目，开箱即用，可[快速上手](https://uniapp.dcloud.io/quickstart)。

## 开发规范

- 页面文件遵循Vue 单文件组件[规范](https://vue-loader.vuejs.org/zh/spec.html)，数据绑定及数据处理支持`vue`规范
- 组件标签靠近小程序[规范](https://uniapp.dcloud.io/component/README)
- 接口API靠近微信小程序[规范](https://uniapp.dcloud.io/api/README)，将前缀`wx`改为`uni`
- 推荐使用`Flex`布局

## 目录结构

┌─common                uni-app公共资源目录
│  └─com-a.js
│  └─com-a.css
┌─components            uni-app组件目录
│  └─comp-a.vue         可复用的a组件
├─hybrid                存放本地网页的目录，[详见](https://uniapp.dcloud.io/component/web-view)
├─platforms             存放各平台专用页面的目录，[详见](https://uniapp.dcloud.io/platform)
├─pages                 业务页面文件存放的目录
│  ├─index
│  │  └─index.vue       index页面
│  └─list
│     └─list.vue        list页面
├─static                存放应用引用静态资源（如图片、视频等）的目录，注意：静态资源只能存放于此
├─wxcomponents          存放小程序组件的目录，[详见](https://uniapp.dcloud.io/frame?id=%E5%B0%8F%E7%A8%8B%E5%BA%8F%E7%BB%84%E4%BB%B6%E6%94%AF%E6%8C%81)
├─main.js               Vue初始化入口文件
├─App.vue               应用配置，用来配置App全局样式以及监听 应用生命周期 [详见](https://uniapp.dcloud.io/frame?id=%E5%BA%94%E7%94%A8%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
├─manifest.json         配置应用名称、appid、logo、版本等打包信息，[详见](https://uniapp.dcloud.io/collocation/manifest)
└─pages.json            配置页面路由、导航条、选项卡等页面类信息，[详见](https://uniapp.dcloud.io/collocation/pages)

> static中的 js文件不会被编译，如果使用ES6将会报错
> css、less等资源放入公共资源目录中

## 生命周期

### 应用生命周期

应用生命周期仅只 `App.vue`中可监听

|函数名|说明|
|:-:|:-:|
|onLaunch|初始化项目完成时触发（仅触发一次）|
|onShow|启动项目或从后台进入前台显示时触发|
|onHide|从前台隐藏至后台时触发|
|onError|程序出错时触发|
|onuniViewMessage|对 nvue 页面发送数据进行监听 [详情](https://uniapp.dcloud.io/use-weex?id=nvue-%e5%90%91-vue-%e9%80%9a%e8%ae%af)|

### 页面生命周期 [详情](https://uniapp.dcloud.io/frame?id=%e9%a1%b5%e9%9d%a2%e7%94%9f%e5%91%bd%e5%91%a8%e6%9c%9f)

|函数名|说明|
|:-:|:-:|
|onLoad|监听页面加载，可接受上个页面传递的数据，param[Object], [详情](https://uniapp.dcloud.io/api/router?id=navigateto)|
|onShow|监听页面显示，每次出现在屏幕时触发|
|onReady|页面初次渲染完成时触发，可能会在页面进入动画完成前触发|
|onHide|从隐藏至后台时触发|
|onUnload|监听页面卸载|
|onResize|监听窗口变化 仅支持 5+App、微信小程序|
|onPullDownRefresh|监听用户下拉动作，[详情](https://uniapp.dcloud.io/api/ui/pulldown)|
|onReachBottom|监听页面上拉触底事件|
|onTabItemTap|点击Tab触发，param[Object]：( &nbsp;{index:String,pagePath:String,text:String} &nbsp;)。仅支持 微信小程序、百度小程序、H5、5+App（自定义组件模式）|
|onShareAppMessage|用户点击右上角分享触发  仅支持 微信小程序、百度小程序、头条小程序、支付宝小程序|
|onPageScroll|监听页面滚动，param[Object]：（ &nbsp; {scrollTop:Number} 页面在垂直方向已滚动的距离（单位px） ）|
|onNavigationBarButtonTap|监听原生标题栏按钮点击事件，param[Object]:(&nbsp; {index:Number} &nbsp;) 原生标题栏按钮数组的下标 仅支持 	5+ App、H5|
|onBackPress |监听页面返回，event对象为 {from:backbutton,navigateBack},backbutton:来源于左上角按钮或android返回键，navigateBack:来源于uni.navigateBack。[详情](https://ask.dcloud.net.cn/article/35120) 仅支持 	5+ App、H5|
|onNavigationBarSearchInputChanged|监听原生标题栏搜索输入框内容变化，param[Object] 仅支持 	5+ App、H5|
|onNavigationBarSearchInputConfirmed|监听原生标题栏搜索输入框搜索事件，点击软键盘上的搜索按钮，param[Object] 仅支持 	5+ App、H5|
|onNavigationBarSearchInputClicked|监听原生标题栏搜索输入框点击事件，param[Object] 仅支持 	5+ App、H5|

- onTabItemTap 常用于点击当前tabItem,滚动或刷新当前页面。



## 路由

`uni-app` 的路由全权由框架统一管理，通过`pages.json`配置每个路由页面路径及样式，不支持 `vueRouter`
其路由有两种方式可跳转：使用 [navigation](https://uniapp.dcloud.io/component/navigator) 组件跳转、调用[API](https://uniapp.dcloud.io/api/router)跳转
框架是以栈的形式管理所有页面，当路由切换时，页面栈的表现详见[详情](https://uniapp.dcloud.io/frame?id=%e9%a1%b5%e9%9d%a2%e6%a0%88)

## 运行环境的判断
- 判断开发生产环境（process.env.NODE_ENV）[详情](https://uniapp.dcloud.io/frame?id=%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83%e5%92%8c%e7%94%9f%e4%ba%a7%e7%8e%af%e5%a2%83)

- 判断平台通过编辑条件判断编译出包后的代码[详情](https://uniapp.dcloud.io/platform),通过API `uni.getSystemInfoSync().platform` 判断客户端环境 android/ios/devtools （小程序）


## 布局

### 单位 [详见](https://uniapp.dcloud.io/frame?id=%e9%a1%b5%e9%9d%a2%e6%a0%b7%e5%bc%8f%e4%b8%8e%e5%b8%83%e5%b1%80)

`uni-app`支持通用单位`px`,`rpx`
`px`：固定的屏幕像素
`rpx`：响应式像素，是相对于基准宽度的单位，可以根据屏幕宽度进行自适应，`uni-app` 规定屏幕基准宽度 750rpx。转化公式（750 * 元素在设计稿中的宽度 / 设计稿基准宽度）

- rpx和宽度相关，屏幕越框，实际像素越大，它不支持动态横竖屏切换计算，如果使用`rpx`建议锁定屏幕方向
- 设计师可用iphone6作为视稿标准
- 如果设计稿不是 750px，HBuilderX提供了工具自动换算，[详见](https://ask.dcloud.net.cn/article/35445)

#### H5单位
- rem: 默认根字体大小为 屏幕宽度/20 (支持 微信小程序、头条小程序、App、H5)
- vh: 1vh等于视窗高度的1%
- vw: 1vw等于视窗宽度的1%

**nvue 不支持百分比，App端，在 pages.json仅支持px**

### 内联样式

静态样式统一写至class中，style只接受动态样式，这样可以避免性能的消耗

目前支持的选择器
class,id,element,::before:/::after(仅微信小程序和5+APP生效)

**无法使用`*`,page相当于body**
App.vue 中的样式为全局样式，nvue不支持全局样式

#### css变量

|变量|描述|
|:-:|:-:|
|--status-bar-height|系统状态栏高度，小程序为25px，H5为0|
|--window-top|内容区域距离顶部的距离，小程序为0 ，H5为NavigationBar高度|
|--window-bottom|内容区域距离底部部的距离，小程序为0 ，H5为TabBar高度|

- 当设置 `navigationStyle:"custom"`取消原生导航栏，由于窗体沉浸式，占据了状态栏位置。此时可使用`var(--status-bar-height)`的view放在页面顶部，避免页面内容出现在状态栏。
- 由于H5端部存在原生导航栏和tabBar,因此由前端模拟
- 目前 nvue 不支持`--status-bar-height`,可在页面onLoad时通过`uni.getSystemInfoSync().statusBarHeight`获取状态栏高度，然后通过style绑定方式给占位view设定高度。

**NavigationBar导航栏( 固定高度`44px` 不可修改),TabBar 底部选项卡( 固定高度`50px` 不可修改)**

#### 背景图注意点

- 支持base64、网络路径图
- 本地路径图小于40Kb,自动转化为base64,大于则须手动转化，或使用网络路径的方式引用。
- 推荐以`~@`的方式引用图片，而V3版本设定为不自动转化成base64
- **微信小程序不支持相对路径**

```css
 .test {
     background-image: url('~@/static/logo.png');
 }
```

##### 字体图

- 网络路径必须加协议头 `https`
- 字体文件大于等于 40kb， 需开发者自己转换，否则使用将不生效；
```css
 @font-face {
     font-family: test1-icon;
     src: url('~@/static/iconfont.ttf');
 }
@font-face {
font-family: 'iconfont';
src: url('https://at.alicdn.com/t/font_865816_17gjspmmrkti.ttf') format('truetype');
}
```

## TypeScript

[Vue.js 对 TypeScript 支持详情](https://cn.vuejs.org/v2/guide/typescript.html)

## [对应小程序自定义组件存放目录](https://uniapp.dcloud.io/frame?id=%e5%b0%8f%e7%a8%8b%e5%ba%8f%e7%bb%84%e4%bb%b6%e6%94%af%e6%8c%81)
