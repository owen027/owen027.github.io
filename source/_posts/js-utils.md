---
title: js-utils
date: 2020-05-28 13:38:43
tags:
---

# 一些功能性函数


## 关于原生JS

### 文件大小单位转换

```js
/**
 * @desc bytesToSize 字节单位换算
 * @param bytes 传入以bit为单位的数据
 */
const bytesToSize = function (bytes) {
    const k = 1024;
    if (!bytes || bytes === 0) return '0 B';
    if (typeof (bytes) == 'string') {
        return bytes
    } else {
        const byte = Math.abs(bytes); //正负数均变正数
        const sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
        const i = Math.floor(Math.log(byte) / Math.log(k));
        return (byte / Math.pow(k, i)).toFixed(2) + sizes[i];
    } 
}
```
### 解析URL后的参数并转换为对象

```js
/**
 * @param {string} url
 * @returns {Object}
 */
export function parseURL(url) {
  const search = url.split("?")[1];
  if (!search) {
    return {};
  }
  return JSON.parse(
    '{"' +
      decodeURIComponent(search)
        .replace(/"/g, '\\"')
        .replace(/&/g, '","')
        .replace(/=/g, '":"')
        .replace(/\+/g, " ") +
      '"}'
  );
}
```
### 生成随机数
```js
/**
 * @returns {string}
 */
export function createUniqueString() {
  const timestamp = +new Date() + "";
  const randomNum = parseInt((1 + Math.random()) * 65536) + "";
  return (+(randomNum + timestamp)).toString(32);
}


```

## 关于 axios 

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



## 关于 Node 

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
