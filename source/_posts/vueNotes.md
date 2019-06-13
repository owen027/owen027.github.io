---
title: vueNotes
date: 2019-06-13 18:17:27
categories: 
- notes
tags:
- vue question
---

###  初始渲染的钩子函数 created  mounted 等

1. 可以取到 data 中的数据，取不到 props 中的数据，可以通过 computed 或者 watch 属性监听变化

2. 如果想要组件或者原生HTML节点重新渲染可以使用 v-if

3. 使用 prototype 挂载自定义方法或插件
```javascript
 Vue.prototype.$bus = Bus; 
 // or
 Vue.use(bus);
```
3.  获取本机 IP 地址
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