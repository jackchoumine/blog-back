---
title: html5 websocket
date: 2019-06-16 17:56:14
tags:
- html5
categories:
- [网络]
---

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行的`全双工通信协议`。

<!--more-->

## 具体使用

WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务器主动向客户端推送数据。

在 WebSocket API 中，浏览器和服务器只需一次HTTP握手，就建立了持久连接，后续可双向通信。

没有出现 websocket 之前，服务器实现推送消息，一般都是 通过 AJAX 轮询，这种方式浪费带宽，频繁的连接、断开，消耗服务器资源。


![@ajax轮询和webSocket的比较|center](https://i.loli.net/2019/01/20/5c447fd150742.png)


建立 websocket 连接后，可主动向对方发送数据，通过 onmessage 事件来接收数据。

创建 webSocket 对象：

```js
let Socket = new WebSoket(url[,protocol]);//
```

url: 可以是 wss 和 ws 协议。
websocket 使用的默认端口和TCP相同，默认80，wss，默认443。
`protocol`：
哪些子协议常用呢？

一个客户端请求例子：

```js
GET / HTTP/1.1

Upgrade: websocket //希望升级到 websocket 协议
Connection: Upgrade //表示升级通信协议
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13 //webSocket 版本
```

Sec-WebSocket-Key：一个随机字符串，服务器会使用这个数据构造出一个SHA-1的信息摘要。`Sec-WebSocket-Key` 加上一个特殊字符串`258EAFA5-E914-47DA-95CA-C5AB0DC85B11`，然后计算摘要，再进行base-64编码，将结果作为响应头`Sec-WebSocket-Accep`的值，这样做的目的是，尽量避免http请求被误认为是websocket协议。

Origin：可选字段，表示发情websocket连接的页面，类似Referer，但是 Origin 包含子协议和主机名。

响应：

```js
HTTP/1.1 101 Switching Protocols

Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```

websocket 属性：

| 属性 | 值   | 说明 | 对应属性 |
| ---- | ---- | ---- | -------- |
|readyState（只读）|0|连接正在建立|连接正在建立|
|readyState|1|连接已建立，可通信|OPEN|
|readyState|2|连接正在关闭|CLOSEING|
|readyState|3|连接已关闭或者连接无法打开|CLOSED|
|bufferedAmount（只读）|数值|正在等待传输的数据的字节数|可用来判断发送是否完毕|

如何监听 readyState 的状态变化呢？
事件：

|属性|处理程序|说明|
|---|--- |---|
|open|onopen|建立连接时触发|
|error|onerror|通信发生错误时触发|
|message|onmessage|接收到数据时触发|
|close|onclose|连接关闭时触发|

以上事件的事件处理函数的参数都是事件`event`,可在事件中获取相关信息。

方法：

|方法|说明|参数|
|-----|----|---|
|send|发送数据|参数只能是字符串或者二进制|
|close|关闭连接|无参数|


二进制数据可用`blod`或者`ArrayBuffer`
```js
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```
还可指定接收的类型：
```js

ws.binaryType = "blob"; // 指定接收的二进制数据的类型
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```
###  参考

[全双工的 websocket](https://halfrost.com/websocket)