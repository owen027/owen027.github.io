---
title: Axios 
date: 2019-04-17 20:40:28
tags:
---
### 原生Ajax
- 异步的JavaScript与XML技术（ Asynchronous JavaScript and XML ）
- Ajax 不需要任何浏览器插件，能在不更新整个页面的前提下维护数据，但需要用户允许JavaScript在浏览器上执行。
#### `XMLHttpRequest` 对象


```javascript
// 创建 构造函数
function Ajax(obj) {
    this.url = obj.url ||'';
    this.type = obj.type || 'get';
    this.data = obj.data ||{};
    this.success = obj.success || null;
    this.error = obj.error || null;
}
// 再原型上创建方法
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

#### 安装
```bash
npm install axios
```

#### methods

##### Get 
```javascript
const axios = require('axios')

axios.get('url?id=xxx')
     .then(res => {
       console.log(res)
     })
     .catch(err =>{
       console.log(err)
     })
//or
axios.get('url',{
  params:{
    id:'xxxxx'
  }
    })
   .then(res =>{
     console.log(res)
   })
   .catch(err =>{
       console.log(err)
     })
```
- 同样的传参方法有 delete
##### post 
```javascript
axios.post('url',{name:'Owen'})
     .then(res =>{
       console.log(res)
     })
     .catch(err =>{
       console.log(err)
     })
```
- 同样的传参方法有  put patch
##### concurrent requests 
```javascript
axios.all([axios.get('url1'),axios.get('url2')])
```

#### API
- `axios(config)`
```javascript

axios({
  method:'get', // default is get 
  url:'url', // request  url
  data:{ // 仅支持post，put和patch方法，数据作为请求主体发送 ( Only the post,put and patch methods are supported, and the data is sent as the request body )
  /* 浏览器仅支持传递 FormData, File, Blob （The browser only supports passing FormData, File and Blob)
     Node 仅支持传递 Stream, Buffer (The Node only supports passing Stream, Buffer)
  */
    name:'owen'
  },
  baseURL:'base/url', // 除非url是绝对路径，否则将baseURL添加到url的前面 (Add baseURL to then front of the url unless the url is an absolute path)
  transformRequest: [function (data, headers) {
    // 可以修改发送的请求数据和请求头，只支持put，post和patch，回调函数必须返回Buffer，ArrayBuffer，FormData或Stream数据
    // Can modify the sent request data and request header,only support put, post and patch.
    // Callback must return Buffer, ArrayBuffer, FormData or Stream data
    
    // Do whatever you want to transform the data

    return data;
  }],
  transformResponse: [function (data) {
     // 修改响应数据，再传递给 then或catch 方法 （Modify the response data and pass it to the then or catch method)
    // Do whatever you want to transform the data

    return data;
  }],
  headers: {'X-Requested-With': 'XMLHttpRequest'}, // 自定义请求头 (Custom request header)
  params:{ // 添加到url尾部的参数，一般用于get 和 delete（ Parameters addde to the end of the url,generally used for get and delete )
    id:'xxx'
  },
   paramsSerializer: function (params) { //序列化 [params] (https://www.npmjs.com/package/qs)
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },
  timeout:1000,// default is 0 , 设置请求超时时间，单位毫秒 （ Set request timeout in milliseconds )
  withCredentials: true, // default is false, 跨域时是否携带cookie（ Whether to carry cookies when crossing domains )
  adapter: function (config) {
    /*拦截响应数据*/
      // At this point:
    //  - config has been merged with defaults
    //  - request transformers have already run
    //  - request interceptors have already run
    
    // Make the request using config provided
    // Upon response settle the Promise
      return new Promise(function(resolve, reject) {
  
    var response = {
      data: responseData,
      status: request.status,
      statusText: request.statusText,
      headers: responseHeaders,
      config: config,
      request: request
    };

    settle(resolve, reject, response);

    // From here:
    //  - response transformers will run
    //  - response interceptors will run

      /**
       * Resolve or reject a Promise based on response status.
       *
       * @param {Function} resolve A function that resolves the promise.
       * @param {Function} reject A function that rejects the promise.
       * @param {object} response The response.
       */
        function settle(resolve, reject, response) {
            var validateStatus = response.config.validateStatus;
            if (!validateStatus || validateStatus(response.status)) {
              resolve(response);
            } else {
              reject(createError(
                'Request failed with status code ' + response.status,
                response.config,
                null,
                response.request,
                response
              ));
            }
          };
        /**
         * Create an Error with the specified message, config, error code, request and response.
         *
         * @param {string} message The error message.
         * @param {Object} config The config.
         * @param {string} [code] The error code (for example, 'ECONNABORTED').
         * @param {Object} [request] The request.
         * @param {Object} [response] The response.
         * @returns {Error} The created error.
         */
        function createError(message, config, code, request, response) {
          var error = new Error(message);
        return enhanceError(error, config, code, request, response);
          }

        /**
         * Update an Error with the specified config, error code, and response.
         *
         * @param {Error} error The error to update.
         * @param {Object} config The config.
         * @param {string} [code] The error code (for example, 'ECONNABORTED').
         * @param {Object} [request] The request.
         * @param {Object} [response] The response.
         * @returns {Error} The error.
         */
        function enhanceError(error, config, code, request, response) {
            error.config = config;
            if (code) {
              error.code = code;
            }

            error.request = request;
            error.response = response;
            error.isAxiosError = true;

            error.toJSON = function() {
              return {
                // Standard
                message: this.message,
                name: this.name,
                // Microsoft
                description: this.description,
                number: this.number,
                // Mozilla
                fileName: this.fileName,
                lineNumber: this.lineNumber,
                columnNumber: this.columnNumber,
                stack: this.stack,
                // Axios
                config: this.config,
                code: this.code
              };
            };
          return error;
        }
    });
  },
  auth:{ //  表示应使用HTTP Basic身份验证，并提供凭据 ( indicates that HTTP Basic auth should be used, and supplies credentials. )
    user:'xxx',
    password:'***'
  },
  responseType: 'json',/* 服务器响应的数据类型（ The server response data type ） 
                         支持 arraybuffer, blob, document, json, text, stream 
                        */
  responseEncoding:'utf8', // 用于解码响应的编码 (Encoding for decoding the response )
  xsrfCookieName: 'XSRF-TOKEN', // default is XSRF-TOKEN , csrf令牌Cookie 名称
  xsrfHeaderName: 'X-XSRF-TOKEN', //default is X-XSRF-TOKEN, xsrf标记值的http标头的名称
onUploadProgress: function (progressEvent) { //上传进度事件 （handling of progress events for uploads ）
    console.log(progressEvent)
  },
onDownloadProgress: function (progressEvent) { // 下载进度事件 （ handling of progress events for downloads）
   console.log(progressEvent)
  },
maxContentLength: 2000, // 允许响应内容的最大字节 （defines the max size of the http response content in bytes allowed）
validateStatus: function (status) { // 返回给定HTTP状态范围, 如果状态在给定范围内，响应数据传给`then` ，否则传给 `catch` ( Returns the given HTTP status range, if the status is within the give range, the respones data is passed to `then`, otherwise passed to `catch` ) 
    return status >= 200 && status < 300; // default
  },
  maxRedirects: 5, // default is 5  // 定义Node 中最大重定向数  ( defines the maximunn number of redirects in Node )
  socketPath: null, //  default is null 定义要在node.js中使用的 UNIX socket
  httpAgent: new http.Agent({ keepAlive: true }), // node 中 http 和 https 的代理
  httpsAgent: new https.Agent({ keepAlive: true }),// http://nodejs.cn/api/http.html
  proxy: { // 代理配置
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
          }
   },
    cancelToken: new CancelToken(function (cancel) { // 取消请求的 token
  })
  })  
  .then(res =>{
       console.log(res)
     })
  .catch(err =>{
       console.log(err)
     })
```
##### 全局配置
- 通过 `axios.create` 方法来替换全局配置
```javascript
const instance = axios.create({
  baseURL: 'base/url'
});
```
- 通过`axios.defaults` 对象替换全局默认配置
```javascript
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
instance.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';

```
##### 拦截器
- 拦截请求前的数据
```javascript
axios.interceptors.request.use(function (config) {
    return config;
  }, function (error) {
    return Promise.reject(error);
  });

```
- 拦截响应数据
```javascript
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```
- 删除拦截器
```javascript
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```
#### 二次封装
```javascript
 /**
  * @desc: axios封装
  * @author: ggw 
  * @module: axios
  * @description: 配合使用 饿了么的 Message和Loading
  * @Date: 2018 
  */
 import axios from 'axios';
 import qs from 'qs';
 import {
     Message,
     Loading
 } from 'element-ui';
 
 import router from '../router'; 
 let loading;
 let headerNone = {
     'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
 };
 let headerTwo = {
     'Content-Type': 'application/json; charset=UTF-8'
 };
 let baseURL = window.location.origin ;


 /** 
  * @description: 定义初始化Loading
  * @method: startLoading 
  */
  const startLoading = () => {
     loading = Loading.service({
         target: '.content-box',
         background: 'rgba(220, 220, 220, 0.51)'
     });
 };
 

 let count = 0;
 /** 
  * @description: 显示Loading 同时多个发送请求 只开启一次Loading
  * @method: showLoading  && hideLoading
  */
  const showLoading = () => {
     if (count === 0) startLoading();
     count++;
 };
  const hideLoading = () => {
     if (count <= 0) return;
     count--;
     if (count === 0) {
         setTimeout(() => {
            loading.close();
         }, 300);
     }
 };

 export let filiter = r => {

     for (let item of Object.keys(r)) {
         if (r[item] === ' ' || r[item] === '') {
             delete r[item];
         }
     }
 };
 /** 
  * @description: 出口
  * @exports api
  * @param:options 必须是对象
  * options 对象为 axios对应参数
  */
 export default (options) => {
     /** 
      * @description: 用来初始化承诺的回调。
      * 这个回调被传递了两个参数：
      * 一个解析回调用一个值或另一个承诺的结果来解析承诺，
      * 以及一个拒绝回调，用来拒绝承诺的原因或错误。
      * @constructor: Promise
      */
     return new Promise((resolve, reject) => {
         const instance = axios.create({
             withCredentials: true,
             headers: headerNone,
             baseURL
         });
         // 请求拦截器
         instance.interceptors.request.use(config => {
              let {load = true} = config.data || config.params || {} ;
             if (load) showLoading();
             //  过滤无值参数
             if (config.params) {
                delete config.params.load;
                 filiter(config.params);
                } else if (config.data) {
                 filiter(config.data);
                delete config.data.load;
                }
             if (
                 config.method.toLocaleLowerCase() === 'post' ||
                 config.method.toLocaleLowerCase() === 'put'
             ) {
                 // json 格式传递
                 if (config.json) {
                     config.headers = headerTwo;
                 } else {
                     config.data = qs.stringify(config.data);
                     config.data = config.data + '&t=' + Date.now();
                 }
             }
             return config;
         }, error => {
              hideLoading();
             return Promise.reject(error);
         });
         // 响应拦截器
         instance.interceptors.response.use(response => {
            setTimeout(hideLoading,0);
             let data;
             // IE9时response.data是undefined，因此需要使用response.request.responseText(Stringify后的字符串)
             if (!response.data ) {
                 data = response.request.responseText;
             } else {
                 data = response.data;
             }

             switch (data.code) { // 接口定义字段
                 case '001':
                     Message({
                         showClose: true,
                         message: data.msg || '未知错误,请联系管理员',
                         type: 'error'
                     });
                     router.push({
                         path: '/login'
                     });
                     break;
                default:
             }
             return data;
         }, err => {
           hideLoading();

             if (err && err.response) {
                 let msg = {
                     400: '请求错误',
                     401: '未授权，请登录',
                     403: '拒绝访问',
                     404: `请求地址出错: ${err.response.request.responseURL}`,
                     408: '请求超时',
                     500: '服务器内部错误',
                     501: '服务未实现',
                     502: '网关错误',
                     503: '服务不可用',
                     504: '网关超时',
                     505: 'HTTP版本不受支持'
                 };
                 let status = parseInt(err.response.status,10);
                 Message({
                     showClose: true,
                     message: msg[status] || '',
                     type: 'error'
                 });
             } else {
                 Message({
                     message: err.config ? `请求地址出错: ${err.config.url}` : err,
                     type: 'error'
                 });
             }

             return Promise.reject(err);
         });
         // 请求
         instance(options)
             .then(res => {
                 resolve(res);
                 return false;
             })
             .catch(error => {
                   reject(error);
             });
     });
 };
```