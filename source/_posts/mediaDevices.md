---
title: 访问媒体设备
date: 2019-08-12 15:54:16
tags:
- mediaDevices
---
## 概览
> mediaDevices 是 Navigator 对象的只读属性，一个单列对象，可以连接访问相机和麦克风，屏幕共享等媒体输入设备

## 方法

### enumerateDevices
> 请求一个可用的媒体输入和输出设备列表，如麦克风、相机、耳机等。返回的 `Promise`完成状态中是一个带有 [MediaDeviceInfo](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo) 的数组

```javascript
let mediaDevices = navigator.mediaDevices
if(!mediaDevices || !mediaDevices.enumerateDevices) return console.erorr('浏览器不支持enumerateDevices API')
navigator.mediaDevices.enumerateDevices()
.then((devices)=>{
    for (let device of devices){
        console.log(device.kind + ': ' +device.lable + ' id = '+ device.deviceId );
    }
})
.catch(err=>{
    console.error(err)
})

/*
audioinput: undefined id = default
audioinput: undefined id = communications
audioinput: undefined id = ac67d348685a08c75e5017f9a449b3d85f08dcb774c88ab95de82bbf2c0fc820
videoinput: undefined id = e41039bcfbc84d926a0b73cdc1d8b1daf3d67d36c62588202191d918fb076426
audiooutput: undefined id = default
audiooutput: undefined id = communications
audiooutput: undefined id = 015d73652e57bffb21679b937675d32c4d4a43862aba3774aaf0b5f1e983151f
*/
```
#### 兼容性
![enumerateDevices](/static/img/enumerateDevices.png)

### getSupportedConstraints
> 返回一个 [MediaTrackSupportedConstraints](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaTrackSupportedConstraints) 对象，其属性都是客户端所支持约束的属性,值为 Boolean 类型

```javascript
let supportedConstraints = navigator.mediaDevices.getSupportedConstraints()
for (let constraint of Object.keys(supportedConstraints)){
    console.log(constraint)
}
/*
aspectRatio
autoGainControl
brightness
channelCount
colorTemperature
contrast
deviceId
echoCancellation
exposureCompensation
exposureMode
exposureTime
facingMode
focusDistance
focusMode
frameRate
groupId
height
iso
latency
noiseSuppression
pointsOfInterest
resizeMode
sampleRate
sampleSize
saturation
sharpness
torch
volume
whiteBalanceMode
width
zoom
*/
```

#### 兼容性
![supportedConstraints](/static/img/supportedConstraints.png)


### getDisplayMedia
> 提示用户选择和授予权限来捕获显示或部分的内容，(如分屏共享时分享哪一屏的内容)然后使用 [medieaStream Recording API](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream_Recording_API) 记录生成的 stream，或作为 [webRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) 会话的一部分进行传输。

可以传递一个[MediaStreamConstraints](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamConstraints) 对象指定返回要求的 [mediaStream](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream)。

```javascript
async function startCapture(displayMediaOptions) {
  let captureStream = null;

  try {
    captureStream = await navigator.mediaDevices.getDisplayMedia(displayMediaOptions);
  } catch(err) {
    console.error("Error: " + err);
  }
  return captureStream;
}

```
#### 兼容性

![displayMedia](/static/img/displayMedia.png)

### getUserMedia
> 提示用户给予使用媒体输入的许可（如麦克风，摄像机）,当媒体输入时产生一个 [mediaStream](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream)包含所请求的媒体类型的轨道。该流可以包括视频轨道（摄像机，视频记录设备，共屏等硬件或虚拟视频流源）、音频轨道（来自麦克风、A/D转换器等硬件或虚拟音频源），也可能是其它轨道类型


该方法返回一个`Promise`对象,成功时 `resolve` 回调函数带有`mediaStream`对象。如果用户拒绝授予使用权限，或是媒体源不可用，则返回 `reject`回调

** `Promise` 可能既不会 `resolve` 也不会`reject`,因为用户不必做出选择，可能只是忽略请求**

![microphone](/static/img/microphone.png)

```javascript
// 想要获取一个最接近 1280x720 的相机分辨率
let constraints = { audio: true, video: { width: 1280, height: 720 } };

navigator.mediaDevices.getUserMedia(constraints)
.then(function(mediaStream) {
  let video = document.querySelector('video');
  video.srcObject = mediaStream;
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) { console.log(err.name + ": " + err.message); });

```
#### 参数 constraints
> 一个[mediaStreamConstraints](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamConstraints)对象指定请求的媒体类型和相对应参数，该对象包含 `video` 和 `audio` 两个属性，必须一个或两个同时被指定，如果无法找到指定的媒体类型或无法满足对于的参数要求，`Promise`将返回 `rejected`

#### 参数配置

##### 1
```javascript
 {audio:true,video:true}
```
**属性设置为 Truthy 则生成的`stream` 必须具有该类型的轨道，否则调用 `getUserMedia`会抛出错误**

##### 2
```javascript
{
  audio: true,
  video: { width: 1280, height: 720 }
}

```
**表示`video`的分辨率应为 1280x720 浏览器将试着满足这个请求参数，如果无法满足要求或选择覆盖，则可能返回其它的分辨率**

##### 3
```javascript
{
  audio: true,
  video: {
    width: { min: 1280 },
    height: { min: 720 }
  }
}
```
**此配置要求了最低分辨率，如果达不到要求，`promise` 将返回 `reject`;还可配置 max、exact(min == max),而且用户将不会得到要求授权的提示**

##### 4
```javascript
{
  audio: true,
  video: {
    width: { min: 1024, ideal: 1280, max: 1920 },
    height: { min: 776, ideal: 720, max: 1080 }
  }
}
```
**如果使用`ideal`,浏览器将尝试找到（如果相机有多个的话）最接近指定值的理想值的设备或相机**
意味着上方的第一个分辨率例子可简写为：
```javascript
{
  audio: true,
  video: {
    width: { ideal: 1280 },
    height: { ideal: 720 }
  }
}
```

##### 5
**并不是所有的 `constraint` 都说数字，如在移动设备上优先使用前置相机**
```javascript
{ audio: true, video: { facingMode: "user" } }

```

**强制使用后置相机**
```javascript
{ audio: true,
 video: {
  facingMode: { exact: "environment" }
   }
}
```
#### APP权限配置
```json
"permissions": {
  "audio-capture": {
    "description": "Required to capture audio using getUserMedia()"
  },
  "video-capture": {
    "description": "Required to capture video using getUserMedia()"
  }
}
```
**作为可能涉及重大隐私问题的API，getUserMedia()规范规定了浏览器有义务满足的各种隐私和安全要求。**
getUserMedia()是一个强大的功能，只能在安全的环境中使用; 在不安全的情境中，`navigator.mediaDevices` 是undefined，阻止访问`getUserMedia()`。
**简而言之，安全上下文是使用`HTTPS`或 `file:///URL` 方案加载的页面，或者是从中加载的页面`localhost`。**
#### 在旧的浏览器中使用新的API
**推荐使用处理了约束的 [adapter.js](https://github.com/webrtc/adapter)`polyfill`  来替代。**
```javascript


// 老的浏览器可能根本没有实现 mediaDevices，所以我们可以先设置一个空的对象
let mediaDevices = navigator.mediaDevices
if (mediaDevices === undefined) {
  mediaDevices = {};
}
// 一些浏览器部分支持 mediaDevices。我们不能直接给对象设置 getUserMedia
// 因为这样可能会覆盖已有的属性。这里我们只会在没有getUserMedia属性的时候添加它。
if (mediaDevices.getUserMedia === undefined) {
  mediaDevices.getUserMedia = function(constraints) {

    // 首先，如果有getUserMedia的话，就获得它
    var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

    // 一些浏览器根本没实现它 - 那么就返回一个error到promise的reject来保持一个统一的接口
    if (!getUserMedia) {
      return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
    }

    // 否则，为老的navigator.getUserMedia方法包裹一个Promise
    return new Promise(function(resolve, reject) {
      getUserMedia.call(navigator, constraints, resolve, reject);
    });
  }
}
mediaDevices.getUserMedia({ audio: true, video: true })
.then(function(stream) {
  var video = document.querySelector('video');
  // 旧的浏览器可能没有srcObject
  if ("srcObject" in video) {
    video.srcObject = stream;
  } else {
    // 防止在新的浏览器里使用它，应为它已经不再支持了
    video.src = window.URL.createObjectURL(stream);
  }
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) {
  console.log(err.name + ": " + err.message);
});

```

#### 兼容性
![getUserMedia](/static/img/getUserMedia.png)

