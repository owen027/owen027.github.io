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

6. `cache`:  请求的 `cache` 模式: `default 、 no-store 、 reload 、 no-cache 、 force-cache` 或者 `only-if-cached` 。

7. `redirect`: 可用的` redirect` 模式:` follow `(自动重定向), `error` (如果产生重定向将自动终止并且抛出一个错误), 或者` manual` (手动处理重定向). 在Chrome中，Chrome 47之前的默认值是 `follow`，从 Chrome 47开始是` manual`。

8. `referrer`: 一个[USVString](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString) 可以是 `no-referrer、client`或一个` URL`。默认是` client`。

9. `referrerPolicy`:指定引用HTTP头的值。可能是一个 ` no-referrer、 no-referrer-when-downgrade、 origin、 origin-when-cross-origin、 unsafe-url 。`

10. `integrity`: 包括请求的  [subresource integrity](https://developer.mozilla.org/zh-CN/docs/Web/Security/%E5%AD%90%E8%B5%84%E6%BA%90%E5%AE%8C%E6%95%B4%E6%80%A7)值 （ 例如：  sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=）。
