---
title: webRTC 搭建信令服务器学习
date: 2020-04-02
categories:
- webRTC
tags:
- signaling serve
---
信令，指为使通信网中各种设备协调运作，在设备之间传递的有关控制信息。用来说明各自的运行情况，提出相关设备的接续要求。

## 通过NodeJs 搭建服务

**Node** 基于V8引擎，是一个 JavaScript 运行环境，可以让JavaScript开发后端程序，打破了JavaScript 只能在浏览器中运行的居局面，同时开发周期短、开发成本低、学习成本低。

V8 JavaScript引擎是由Google公司使用C++语言开发的一种高性能JavaScript引擎

###  Node 具有超高并发能力

在Java、PHP或者.NET等服务器语言中，会为每一个客户端连接创建一个新的线程。而每个线程需要耗费大约2MB内存，一个8GB内存的服务器可以同时连接的最大用户数为4000个左右。NodeJs仅使用一个线程，当有用户连接了，就触发一个内部事件，通过非阻塞I/O、事件驱动机制，，让Node.js程序宏观上也是并行的。使用Node.js，一个8GB内存的服务器，可以同时处理超过4万用户的连接。

 Nodejs 的生态链非常的完整，有各种各样的功能库。可以根据自己的需要通过安装工具 NPM 快速安装

##  使用[socket.io](https://socket.io/docs/) 实现信令服务器

### 环境搭建
 官网下载 node (https://nodejs.org/en/)
 安装 socket.io
```git
npm install socket.io
npm install node-static
```
socket.io特别适合用来开发WebRTC的信令服务器，通过它来构建信令服务器特别的简单，这主要是因为它内置了**房间** 的概念。

socket.io 分为服务端和客户端两部分。服务端由 Nodejs加载后侦听某个服务端口，客户端要想与服务端相连，首先要加载 socket.io 的客户端库，然后调用 `io.connect();`就与服务端连上了。

### socket.io API
```js
//给本次连接发消息
socket.emit()

//给某个房间内所有人发消息
io.in(room).emit()

//除本连接外，给某个房间内所有人发消息
socket.to(room).emit()

//除本连接外，给所以人发消息
socket.broadcast.emit()


//发送 command 命令
 socket.emit('cmd’);
// 接收 cmd
 socket.on('cmd',function(){...});

//送了一个 command 命令，带 data 数据
 socket.emit('action', data);
 socket.on('action',function(data){...});

// 发送了command命令，还有两个数据
 socket.emit(action,arg1,arg2);
 socket.on('action',function(arg1,arg2){...});

```

**客户端 demo**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>WebRTC client</title>

</head>
<body>
    <div id="app">

    </div>
</body>

<script src="/socket.io/socket.io.js"></script>
<script type="module" src="./socketIo.js"></script>
</html>

```

```js
// socketIo.js
/*
用户写入要加入的房间,然后，通过 io.connect() 建立与服务端的连接，
根据socket返回的消息做不同的处理
 */
 window.onload = init
function init (){

   let room = prompt('输入房间名')
    if(!room) {
        alert('房间名不能为空')
        return false
    }

    var socket = io.connect()

    console.log('Joining room: '+ room )

    socket.emit('create or join',room)

    socket.on('join',function(room){ // 监听 服务器返回的消息
        console.log('Making request to join room '+ room + ' is empty', 'You are the initiator')
        })

    socket.on('created',function(room){
        console.log('created '+ room )
        })
    socket.on('joined',function(room){
            console.log('joined '+ room )
            })
    socket.on('full',function(room){
    console.log('Room '+ room + ' is full')
    })

    socket.on('empty',function(room){
        console.log('Room '+ room + ' is empty')
        })

    socket.on('log',function(array){
            console.log.apply(console,array)
            })

}


```

**服务端 demo**

```js
/* socke.io 信令服务器
引入 node-static 库，发布静态文件
客户端（向服务端发起请求时，服务器通过该模块获得客户端运行的代码

最后通过 运行 node server.js 启动node服务
*/
const static = require('node-static')
const http = require('http')
const file = new (static.Server)()
const  app = http.createServer((req,res)=>{
    file.serve(req,res)
}).listen(9800)
const Io = require('socket.io').listen(app) // 侦听 9800 端口

Io.sockets.on('connection',socket =>{

 const log = (...arg)=>{
     const array = ['>>> Message from server: ']
     for ( let v of arg){
         array.push(v)
     }
     socket.emit('log',array)
    }
    socket.on('message',(message)=>{
        log('Got message: ',message)
        socket.broadcast.emit('message',message) // 给所有房间发送信息
    })

    socket.on('message',(message)=>{
        log('Got message: ',message)
    })
    socket.on('create or join',room =>{
        let clientsInRoom = Io.sockets.adapter.rooms[room]
        let numClients = clientsInRoom?Object.keys(clientsInroom.sockets).length:0 // 房间人数
        log(`Room ${room} has ${numClients} client(s) -- `,`Request to create or join room ${room}`)
        switch(true){
            case  numClients === 0:
                socket.join(room)
                socket.emit('created',room)
            break;
            case  numClients === 1:
                Io.sockets.in(room).emit('join',room) // 向对应的房间推送消息
                socket.join(room) // 加入房间
                scoket.emit('joined',room) // 向客户端推送消息
                break;
            default:
                // 最多两人
                socket.emit('full', room); //发送 "full" 消息
        }
        console.log('created or join end')

        let message = `emit(): client ${socket.id} joind room ${room}`
        socket.emit(message)
        socket.broadcast.emit(message)

    })

})


```
参考
[WebRTC 入门教程（一）| 搭建WebRTC信令服务器](https://webrtc.org.cn/webrtc-tutorial-1-setup-signaling/)

