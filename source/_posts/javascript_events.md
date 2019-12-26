---
title: javascript_events
date: 2019-12-18 13:55:26
categories:
- notes
tags:
- JS_events
---
## 事件
事件是某事发生的信号，它可以被系统侦测到，从而触发某个函数或程序

DOM 事件标准描述了事件传播的 3 个阶段：

- 捕获阶段 —— 事件（从 Window）向下到达元素上。
- 目标阶段 —— 事件到达目标元素。
- 冒泡阶段 —— 事件从元素上开始冒泡。

事件首先通过祖先链向下传递到元素（捕获），然后到达目标，最后上升（冒泡），在途中调用处理器。

### 冒泡

冒泡：当事件发生再元素上，它首先会运行元素本身的处理器，然后运行父级上同类型的处理器，之后运行其它祖级上同类型的处理器(大多数事件都是冒泡行为，forcus等例外)；可通过 `event.stopPropagation()`
通过 JS`on<event>`属性、`addEventListener(event,handler,false)`或HTML属性添加的函数，发生在冒泡阶段和目标阶段

```html
<style>
  body * {
    margin: 10px;
    border: 1px solid blue;
  }
</style>

<div onclick="alert('forbear')">FORBEAR
  <div onclick="alert('parent')">Parent
    <p onclick="alert('son')">SON</p>
  </div>
</div>

```


JS中与鼠标相关事件：`click`左击、`dblclick`左双击、`contextmenu`右击、`mouseover / mouseenter(不会冒泡)`鼠标移入元素、`mousemove / mouseleave(不会冒泡)`鼠标移出元素、`mousedown`按下、`mouseup`松开

