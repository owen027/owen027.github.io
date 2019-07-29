---
title: vueNotes
date: 2019-06-13 18:17:27
categories: 
- notes
tags:
- vue question
---

##  vue 框架中遇到的问题

1. 可以取到 data 中的数据，取不到 props 中的数据，可以通过 computed 或者 watch 属性监听变化

2. 如果想要组件或者原生HTML节点重新渲染可以使用 v-if

3. 使用 prototype 挂载自定义方法或插件
```javascript
 Vue.prototype.$bus = Bus; 
 // or
 Vue.use(bus);
```
## axios 问题
1. 接受二进制流文件乱码

## Node 中的问题
1. 获取本机 IP 地址
```javascript
const os = require('os');
const ip = showObj(os.networkInterfaces());

function showObj(obj){
/*     for (let devName in obj){
        let iface = obj[devName];
        for (let i = 0;i < iface.length;i++){
            let alias = iface[i];
            if (alias.family === 'IPv4' 
                && alias.address !== '127.0.0.1' 
                && !alias.internal){
                return alias.address;
            }
        }
    } */
     for (let devName in obj){
             let iface = obj[devName];
             for (let alias of iface ){
                if ( alias.family === 'IPv4'  && alias.address !== '127.0.0.1'  && !alias.internal) return alias.address;
             }
     }
}
```

## 开发方式
**当我们确定组件层级时，最容易的方式就是先用已有的数据模型渲染一个不包含交互功能的UI。最好将渲染UI 和添加交互两个过程分开**

因为，编写一个应用的静态版本时，往往需要编写大量代码，不需要考虑太多交互细节；
添加交互细节功能时则需考虑大量细节，而不需要编写太多代码。
所以将两个过程分开进行更为合适。
1. 自上而下
首先编写较高的组件，对于简单应用使用这种方式更加方便

2. 自下而上
对于复杂应用先编写较为底层的组件，同时为底层组件编写测试的方式更加简单
