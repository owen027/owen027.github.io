---
title: Axios 
date: 2019-04-17 20:40:28
tags:
---
### 原生ajax

#### `XMLHttpRequest` 对象


```javascript

function Ajax(obj) {
    this.url = obj.url ||'';
    this.type = obj.type || 'get';
    this.data = obj.data ||{};
    this.success = obj.success || null;
    this.error = obj.error || null;
}

Ajax.prototype.send = function(){
    var self = this;
    var  toStr = Object.prototype.toString; 
    if (self.data === null && typeof self.data !== 'object' && Array.isArray(obj)) return;
    return (function(){
            // 实例化 XML对象
            var xhr = new XMLHttpRequest();
            var data = '';
            // 序列化参数
            for (var k in self.data){
                    data += k + '=' + self.data[k] + '&';
            }
            data = data.substr(0,data.length - 1);
            // 接收回调函数             
            xhr.onreadystatechange = function(){
                if (xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                        isFunction(self.success)  &&  self.success(xhr.responseText)
                    }else{
                        isFunction(self.error)  && self.error(xhr)
                    }
                }
            }
            // 初始化请求
            if(self.type.toLocaleLowerCase() === 'post'){
                    xhr.open ('post',self.url,true)
                    // 设置请求头
                    xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
                    //发送请求
                    xhr.send(data)
                } else {
                    xhr.open('get', self.url + "?" + data,true)
                    xhr.send(null)
            }
    }());
};

function isFunction(obj){
    return toStr.call(obj) === "[object Function]"
}
var ajax = new Ajax({
     type:'post',
     url:"/login",
     data:{ 
         loginname:"admin",
         password:"admin" 
        },
      success:function(e){
            console.log(e)
            }, 
       error:function(err){
              console.log(err)
              },
        }).send();
```
####  `XMLHttpRequest Level 2` 相比于 老版本的 `XMLHttpRequest` 新增以下内容：

##### 可以设置 HTTP 请求超时时间
  ```javascript
   var xhr = XMLHttpRequest();
   xhr.open('GET'.'url');
   // 超时 2s
   xhr.timeout = 2000;
   // 超时处理
   xhr.ontimeout = function(e) {
       console.log(e)
   }
   xhr.send(null)
  ```
##### 可以通过 `FormData` 发送表单数据
  ```javascript
     // 实例化 FormData
   var formData = new FormData();
    // 添加数据
    formData.append(key,value);

    xhr.open('POST','url');
    xhr.send(formData);

  ```
##### 可以上传文件
 - `FormData` 除了可以添加字符串数据，也可以添加 `blob、file` 类型的数据，因此可以用于上传文件。
 - 在浏览器中，一般是通过文件上传输入框来获取 file 对象，比如：
  ```html
  <input type="file" name='uploadFile' id="upload-file" />
    
  ```
  ```javascript
  document.getElementById('upload-file')
          .addEventListener('change', function () {
              
              var formData = new FormData();
              // 获取数据
                formData.append('uploadFile', this.files[0])
                 xhr.send(formData)
        })
  ```
##### 支持跨域请求
 - 浏览器默认是不允许跨域请求的，有时候又是必要的，在以前通常使用[`JSONP`](https://baike.baidu.com/item/JSONP)来解决（IE10 以下不支持）
 - 为了标准化跨域请求， W3C提出 [跨域资源共享](http://www.ruanyifeng.com/blog/2016/04/cors.html)（CORS）前端无须修改代码，只需 服务器返回 `Access-Control-Allow-Origin` 响应头，指定允许对应的域
 - `CORS` 默认不发送 `cookie` 如果需要发送，前端需要设置 `withCredentials `属性，同时服务器需要 返回 `Access-Control-Allow-Credentials: true`,
 ```javascript
  xhr.withCredentials = true;
 ```
##### 可以获取服务端二进制数据
  1. 使用 `overrideMimeType` 方法覆写服务器指定的 [`MIME`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) 类型，从而改变浏览器解析数据的方式
  ```javascript
  // 参数 MIME 类型
  // 告诉浏览器，服务器响应的内容是用户自定义的字符集 
  xhr.overrideMimeType('text/plain; charset=x-user-defined');
  // 浏览器就会将服务器返回的二进制数据当成文本处理，我们需要做进一步的转换才能拿到真实的数据
    // 获取二进制数据的第 i 位的值
    var byte = xhr.responseText.charCodeAt(i) & 0xff
  ```
  -  "& 0xff" 运算 参考 [阮一峰的文章](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)
  2. xhr.responseType 用于设置服务器返回的数据的类型,将`返回类型设置为 blob 或者 arraybuffer`，然后就可以从 `xhr.response` 属性获取到对应类型的服务器返回数据。
  ```javascript
    xhr.responseType = 'arraybuffer'
    xhr.onload = function () {
    var arrayBuffer = xhr.response
    // 接下来对 arrayBuffer 做进一步处理...
    }
  ```
##### 可以获取数据传输进度信息 [参考资料](https://dev.opera.com/articles/xhr2/)

  - 使用  onload 监听了一个数据传输完成的事件。
    ```javascript
    // 上传进度监听
    xhr.upload.addEventListener('progress', onProgressHandler, false);

    // 传输成功完成
    xhr.upload.addEventListener('load', onLoadHandler, false);
    // 传输失败信息
    xhr.upload.addEventListener('error', onErrorHandler, false);

    ```
#### 兼容性 
![XMLHttpRequest](/static/img/xml.png)

更多资料参考:
[阮一峰的文章](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)


---

 
### Axios 
- 基于 [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 Http 库 
- 可以在客户端 和 nodeJs中使用
- 在客户端创基 XMLHttpRequests
- 在nodeJs 创建 [HTTP](https://nodejs.org/api/http.html) 请求
- 支持Promise
- 可拦截转化请求和响应数据
- 取消请求
- 自动转化JSON数据
- 支持客户端 [XSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

#### 兼容性
![axios](/static/img/axios.png)
