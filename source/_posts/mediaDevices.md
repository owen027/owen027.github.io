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
> 提示用户给予使用媒体输入的许可（如麦克风，摄像机）
![microphone](/static/img/microphone.png)