---
title: vueNotes
date: 2019-06-13 18:17:27
categories:
- notes
tags:
- vue question
---

##  vue 框架中遇到的问题

###可以取到 data 中的数据，取不到 props 中的数据，可以通过 computed 或者 watch 属性监听变化

### 如果想要组件或者原生HTML节点重新渲染可以使用 v-if

### 使用 prototype 挂载自定义方法或插件
```javascript
 Vue.prototype.$bus = Bus;
 // or
 Vue.use(bus);
```
## axios 问题

### 接收二进制流文件乱码问题。
**1. 须将axios 配置中的`responseType`设置为'arraybuffer'，这样就不会让表格出现乱码现象；**
**2. 如果要动态设置文件名则需要让后台将名字设置到响应头中，否则将是一个乱码的文件名；**
**3. 然后通过`<a></a>` 标签的特性来，自动点击下载文件；**
**4. 如果要兼容IE则需要利用`navigator.msSaveOrOpenBlob`方法；**
**5. 兼容Firefox 须将`<a></a>` 标签添加到`body`中，最后再移除`<a></a>` 标签**

```javascript
// axios config
 config = {
     responseType: 'arraybuffer'
    }

// 返回数据处理
getUserInfoExport(data).then(({data,headers}) => {
        let blob = new Blob([data], { type: 'application/vnd.ms-excel' }) // 将服务端返回的文件流（二进制）excel文件转化为blob
        let fileName = headers.filename

        if (window.navigator && window.navigator.msSaveOrOpenBlob) { // IE10+
          window.navigator.msSaveOrOpenBlob(blob, fileName)
        } else {
          let objectUrl = (window.URL || window.webkitURL).createObjectURL(blob)
          let downFile = document.createElement('a')
          downFile.style.display = 'none'
          downFile.href = objectUrl
          downFile.download = fileName // 下载后文件名
          document.body.appendChild(downFile)
          downFile.click()
          document.body.removeChild(downFile) // 下载完成移除元素
          // window.location.href = objectUrl
          window.URL.revokeObjectURL(objectUrl) // 只要映射存在，Blob就不能进行垃圾回收，因此一旦不再需要引用，就必须小心撤销URL，释放掉blob对象。
        }
      })
```
[参考连接](https://www.w3.org/TR/FileAPI/#url)

## Node 中的问题
### 获取本机 IP 地址
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
### 自上而下
首先编写较高的组件，对于简单应用使用这种方式更加方便

### 自下而上
对于复杂应用先编写较为底层的组件，同时为底层组件编写测试的方式更加简单
