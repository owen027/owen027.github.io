---
title: JavaScript 单例模式 （Singleton Pattern）
date: 2019-07-21 23:46:04
categories:
- design patterns
tags:
- Singleton
---


## 概念
> 确保只有一个实例，并提供全局访问。

## 实现思路
一个类能返回对象一个引用（永远是同一个）和一个获得该实例的方法（必须是静态方法，通常命名为`getIntance`）；当我们调用这个方法时，类持有的引用不为空则返回这个引用，如果为空须创建该类实例并将实例的引用赋予该类保持的引用；同时将该类的构造函数定义为私有方法，那么其他环境就无法通过调用该类的构造函数来实例化该类的对象，只能通过该类提供的静态方法得到该类唯一的实例。

### 实现 Java 语言中的单例模式

```Java
  public class Singleton {
      private static final Singleton {
          private Singleton() { };

          public static Singleton getInstance {
              if (INSTANCE == null) {
                  synchronized(Singleton.class) {
                      if(INSTANCE = null) {
                         INSTANCE = new Singleton()
                      }
                  }

              }
              return INSTANCE;
          }
      }
  }
```
### 实现 JavaScript 语言中的单例模式
```javascript
let Singleton = function(name){
    this.name = name;
}
Singleton.prototype.getName = function() {
    console.log(this.name)
}
Singleton.getInstance = (function() {
   let instance;
   return function(name) {
       if(instance) return instance;
       return instance = new Singleton(name)
   }
})()
let s1 = Singleton.getInstance('owen'); // Singleton {name: "owen"}
let s2 = Singleton.getInstance('guowen'); // Singleton.getInstance('guowen');
s1 === s2 // true

```
> JavaScript中单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。
## 应用

### 命名空间
使用命名空间可以降低全局变量带来的命名污染；
最简单的方法是对象字面量
```javascript
const globalWeb = {
    a() {},
    b() {}
    // ...
}
```
或者使用闭包
```javascript
let Singleton = (function(){
    let instance;
    let init = function() {
    let name = 'owen';
        return {
            name,
            data(){
                return {}
            },
            method:{ }
        }
    }
    return {
        getInstance() {
            if(instance) return instance;
            return instance = init()
        }
    }
}())
let app = Singleton.getInstance() // {name: "owen", data: ƒ, method: {…}}
```

### 惰性单例
> 惰性单例指在需要的时候才创建对象实例，在实现开发中非常有用，即目标对象只有在使用的时候才被创建，而不是页面加载好时创建。

#### 模态框示例
 点击一个按钮弹窗一个模态框，很明显页面是唯一的，一次不会弹窗多个模态框的情况

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>modal box</title>
    <style>
    * {
        margin: 0;
        padding:0;
    }
   html{
   width:100%;
   height:100%;
   }
    .Owen {
        width:30%;
        height:30%;
        margin:10% auto;
    }
    #modal {
        width:100%;
        height:100%;
        position:fixed;
        left:0;
        top:0;
        background: rgba(0, 0, 0, 0.52);
        display:none;
    }
    .main{
        width:30%;
        height:30%;
        margin:10% auto;
        text-align: center;
        background-color: #b0e8ff;
    }
    </style>
</head>
<body>
   <div class="Owen">
        <button>Owen</button>
   </div>
    <div id="modal">
        <div class="main">
          <div>
                我是弹框
          </div>
        </div>
    </div>
</body>
<script>

window.onload = function(){
    let openModal = document.querySelector("button")
    let modal = document.querySelector("#modal")
    openModal.addEventListener('click',function(){
       modal.style.display = 'block'
    })
}

</script>
</html>
```
 第一种方法是在页面加载完成时创建好这个弹框，一开始就是隐藏的，只有点击按钮的时候才显示，这种方式有一个问题，就是我们进入页面，只是看看其他内容，不做任何操作；这样就造成资源浪费

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>modal box</title>
    <style>
    * {
        margin: 0;
        padding:0;
    }
   html{
   width:100%;
   height:100%;
   }
    .Owen {
        width:30%;
        height:30%;
        margin:10% auto;
    }
    #modal {
        width:100%;
        height:100%;
        position:fixed;
        left:0;
        top:0;
        background: rgba(0, 0, 0, 0.52);
    }
    .main{
        width:30%;
        height:30%;
        margin:10% auto;
        padding:20px;
        text-align: right;
        background-color: #b0e8ff;
        position:relative;
    }
    .main div {
        text-align: center;
    }
    .main span {
        display: inline-block;
        padding:5px;
        cursor:pointer;
    }
    </style>
</head>
<body>

    <div class="Owen">
        <button>点我</button>
    </div>

</body>
<script>
window.onload = function(){
    init()
}
function init() {
    let openModal = document.querySelector("button")
    let createModal =( function() {
        let flag;
        // 生成 Modal 容器
        let div = document.createElement('div')
        div.id = "modal"
        div.style.display = "none";
        return function() {
            if(flag) return div;
            flag = true;
            let fra = document.createDocumentFragment();
            // 添加 Modal 内容
            els = `<div class="main">
                    <span class="close">×</span>
                        <div>
                            我是弹框
                        </div>
                    </div>
                    `
            div.innerHTML = els;
            fra.appendChild(div)
            document.body.appendChild(fra)
            // 关闭 Modal
            let close = document.querySelector('.close')
            close.addEventListener('click',function(){
                flag = false;
                document.body.removeChild(div)
            })

            return div
        }
    }())
    // 显示 Modal
    openModal.addEventListener('click',function(){
       createModal().style.display = "block";
    })
}
</script>
</html>
```
第二种方法，只执行一次DOM的创建修改操作，不用频繁的创建和删除节点，提高资源利用率；


**参考资料**

《JavaScript设计模式与开发实践》
[基维](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F)

[大叔](https://www.cnblogs.com/TomXu/archive/2012/02/20/2352817.html)