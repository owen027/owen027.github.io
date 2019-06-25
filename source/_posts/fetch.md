---
title: fetch
date: 2019-06-24 16:15:59
categorise:
- javascrip
tags:
- fetch
---
# fetch
- 一个获取资源的接口，类似于[ajax](https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX)
- 是基于 `Promise`之上设计，旧版本IE 完全不支持，须借助 [polyfill](https://github.com/stefanpenner/es6-promise)来兼容
- 提供了对 Request 和 Response （以及其他与网络请求有关的）对象的通用定义
- 发送请求或者获取资源，需要使用 window.fetch or WindowOrWorkerGlobalScope.fetch 方法。

## 参数

### 资源路径（url string）

- 他必须接收一个需要请求的资源路径，返回一个`promise`对象，请求成功的数据返回到[Responese](https://developer.mozilla.org/zh-CN/docs/Web/API/Response/Response)回调中，请求失败的信息返回到 [Request](https://developer.mozilla.org/zh-CN/docs/Web/API/Request)中。
- 当接收到一个代表错误的 HTTP状态码时，`fetch`返回的`promise`不会被标记为 `reject`而会被标记为`resolve`，比如状态码为 404，500.只有网络故障或请求被阻止时才被标记为`reject`

```javascript
fetch('https://api.apiopen.top/musicDetails1')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson); //{code: 400, message: "404 Not Found", result: "https://api.apiopen.top/musicDetails1"}
  })

```
- `fetch` 默认是不会从服务端发送接收或发送任何 `cookie`,如果需要则必须设置 [credentials](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch#%E5%8F%82%E6%95%B0),自 2017/8 起默认的credentials政策变更为same-originFirefox也在61.0b13中改变默认值

### [, config]

- 配置项对象，包括所有对请求的设置

1. `method`: 请求使用的方法，如 GET、POST。

2. `headers`: 请求的头信息，形式为 [Headers](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers) 的对象或包含 [ByteString](https://developer.mozilla.org/zh-CN/docs/Web/API/ByteString)值的对象字面量。

3. `body`: 
    请求的 `body `信息：
    可能是:
     [Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)（ 表示一个不可变、原始数据的类文件对象）、`BufferSource` （ 用于表示自身为ArrayBuffer或者TypedArray提供对象的对象ArrayBufferView。）、
     [FormData](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)（表示表单数据的键值对的构造方式，经过它的数据可以使用` XMLHttpRequest.send()` 方法送出，本接口和此方法都相当简单直接。如果送出时的编码类型被设为 "multipart/form-data"，它会使用和表单一样的格式。）、
     [URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams) （接口定义了一些实用的方法来处理 URL 的查询字符串） 
     或者 `USVString` 对象。
      <label >注意` GET` 或` HEAD `方法的请求不能包含 body 信息。</label>

4. `mode`: 请求的模式，如 `cors、 no-cors` 或者 `same-origin`。

5. `credentials`: 请求的 `credentials`，如 `omit、same-origin` 或者 `include`。为了在当前域名内自动发送 cookie ， 必须提供这个选项， 从 Chrome 50 开始， 这个属性也可以接受 [FederatedCredential](https://developer.mozilla.org/zh-CN/docs/Web/API/FederatedCredential) 实例或是一个 [PasswordCredential](https://developer.mozilla.org/zh-CN/docs/Web/API/PasswordCredential) 实例。

    - 如果需要跨域请求需设置为 "include"
    - 如果只在同域内发送cookie 则设置为 "same-origin"
    - 如果任何情况都不发送cookie 则设置为 "omit"
6. `cache`:  请求的 `cache` 模式: `default 、 no-store 、 reload 、 no-cache 、 force-cache` 或者 `only-if-cached` 。

7. `redirect`: 可用的` redirect` 模式:` follow `(自动重定向), `error` (如果产生重定向将自动终止并且抛出一个错误), 或者` manual` (手动处理重定向). 在Chrome中，Chrome 47之前的默认值是 `follow`，从 Chrome 47开始是` manual`。

8. `referrer`: 一个[USVString](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString) 可以是 `no-referrer、client`或一个` URL`。默认是` client`。

9. `referrerPolicy`:指定引用HTTP头的值。可能是一个 ` no-referrer、 no-referrer-when-downgrade、 origin、 origin-when-cross-origin、 unsafe-url 。`

10. `integrity`: 包括请求的  [subresource integrity](https://developer.mozilla.org/zh-CN/docs/Web/Security/%E5%AD%90%E8%B5%84%E6%BA%90%E5%AE%8C%E6%95%B4%E6%80%A7)值 （ 例如：  sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=）。

```javascript
const Fetch = function (url,config){
    if(typeof(config) !== 'object' || config === null) return throw `Config needs to pass an object type`
  let data = config || {} ;
  let {method = 'GET', param = null, mode = "cors", cache = "no-cache",headers = {'Access-Control-Allow-Origin': '*',
    'content-type': 'application/json'}, redirect = "follow", credentials = "include", referrer = "no-referrer"} = data;
  /*  // 传输 JSON 数据 需将 param 转换 
    JSON.stringify(param)
    //上传文件 需传输 formData 格式
    let formData = new FormData()
    let fileField = document.querySelector("#myFile")
    formData.append('title',"My File")
    formData.append('fileField ',fileField .files[0])
    
  */

  return  fetch(url,{
   method:method.toUpperCase(),
   body:param,
   mode,
   cache,
   headers,
   redirect,
   credentials,
}).then(res =>{
   if(res.ok) return res.json() 
    throw new Error("Network response fail："+res.status)
}
).catch(err=>console.error(err))
}

Fetch('https://api.apiopen.top/musicDetails1',{credentials:'omit'}).then(res =>console.log(res)).catch(err=>console.error(err))

```

### Headers constructor

- 创建一个 [headers](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers) 对象

#### 一个 headers 对象是一个简单的多名值对：
```javascript
let content = "Hello World";
let myHeaders = new Headers();
myHeaders.append("Content-Type", "text/plain");
myHeaders.append("Content-Length", content.length.toString());
myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```

#### 可以传一个多维数组或者对象字面量：
```javascript
let content = "Hello World";
let myHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});

//获取和设置
console.log(myHeaders.has("Content-Type")); // true
console.log(myHeaders.has("Set-Cookie")); // false
myHeaders.set("Content-Type", "text/html");
myHeaders.append("X-Custom-Header", "AnotherValue");
 
console.log(myHeaders.get("Content-Length")); // 11
console.log(myHeaders.getAll("X-Custom-Header")); // ["ProcessThisImmediately", "AnotherValue"]
 
myHeaders.delete("X-Custom-Header");
console.log(myHeaders.getAll("X-Custom-Header")); // [ ]
```

如果使用了一个不合法的HTTP Header属性名，那么Headers的方法通常都抛出 TypeError 异常。如果不小心写入了一个不可写的属性，也会抛出一个 TypeError 异常。除此以外的情况，失败了并不抛出异常。

#### 检查 content type 是否正确 
```javascript
fetch(myRequest).then(function(response) {
  if(response.headers.get("content-type") === "application/json") {
    return response.json().then(function(json) {
      // process your JSON further
    });
  } else {
    console.log("Oops, we haven't got JSON!");
  }
});
```

### Response 对象
- `fetch` 返回的对象

#### 对象中常用的属性
1. status： 响应状态码 如 200 404 等
2. statusText：返回和状态码对应信息
3. ok 检查状态码是否 在 200-299之间，返回true or false

## 检查环境支持度
```javascript
if(this.fetch) {
    // run my fetch request here
} else {
    // do something with XMLHttpRequest?
}
```

## 兼容性
![fetch](/static/img/fetch.png)

[Fetch规范](https://fetch.spec.whatwg.org/)