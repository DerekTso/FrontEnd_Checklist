# HTML5基础 - WebSocket

## 目录
- [什么是 WebSocket](#什么是-websocket)
- [如何建立 WebSocket 连接](#如何建立-websocket-连接)
- [一个典型的 Websocket 握手请求](#一个典型的-websocket-握手请求)
- [创建 WebSocket 对象](#创建-WebSocket-对象)
- [WebSocket 实例](#websocket-实例)

### 什么是 WebSocket

1. HTTP 协议是一种无状态的、无连接的、单向的应用层协议。它采用了请求 / 响应模型。通信请求只能由客户端发起，服务端对请求做出应答处理
2. WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议
3. WebSocket 协议本质上是一个基于 TCP 的协议
4. 在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输（服务器端可以主动发送信息给客户端）

### 如何建立 WebSocket 连接

1. 客户端浏览器首先要向服务器发起一个 HTTP 请求
2. 这个请求和通常的 HTTP 请求不同，它包含了附加头信息 "Upgrade: WebSocket"，表明这是一个申请协议升级的 HTTP 请求
3. 服务器端解析这些附加的头信息然后产生应答信息返回给客户端
4. 客户端和服务器端的 WebSocket 连接就建立起来了，双方就可以通过这个连接通道自由的传递信息
5. 这个连接会持续存在直到客户端或者服务器端的某一方主动的关闭连接

### 一个典型的 Websocket 握手请求

* 客户端请求

```
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13
```

* 服务器回应

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```

1. Connection 必须设置 Upgrade，表示客户端希望连接升级
2. Upgrade 字段必须设置 Websocket，表示希望升级到 Websocket 协议
3. Sec-WebSocket-Key 是一个 Base64 encode 的值，由浏览器随机的生成，服务器端会用这些数据来构造出一个 SHA-1 的信息摘要，将结果做为 “Sec-WebSocket-Accept” 头的值，返回给客户端
4. Sec-WebSocket-Version 表示支持的 Websocket 版本。RFC6455 要求使用的版本是 13
5. Origin 字段是可选的，通常用来表示在浏览器中发起此 Websocket 连接所在的页面，类似于 Referer。但是，与 Referer 不同的是，Origin 只包含了协议和主机名称
6. 其他一些定义在 HTTP 协议中的字段，如 Cookie 等，也可以在 Websocket 中使用

### 创建 WebSocket 对象

```var Socket = new WebSocket(url, [protocol] );```

1. 第一个参数 url, 指定连接的 URL
2. 第二个参数 protocol 是可选的，指定了可接受的子协议

// WebSocket 属性

* Socket.readyState(只读属性/表示连接状态)

    - 0 - 表示连接尚未建立
    - 1 - 表示连接已建立，可以进行通信
    - 2 - 表示连接正在进行关闭
    - 3 - 表示连接已经关闭或者连接不能打开

* Socket.bufferedAmount(只读属性/还没有发出的 UTF-8 文本字节数)

// WebSocket 方法

- Socket.send()     使用连接发送数据
- Socket.close()    关闭连接


### WebSocket 实例

```
function WebSocketTest() {
    if ("WebSocket" in window) {
// 打开一个 web socket
var socket = new WebSocket("ws://localhost:9000");
socket.onopen = function() {
    // Web Socket 已连接上，使用 send() 方法发送数据
    socket.send("发送数据");
};
socket.onmessage = function (evt)  {
    // 客户端接收到服务端的数据
    var received_msg = evt.data;
};
socket.onerror = function() {
    // websocket 发生错误
};
socket.onclose = function() {
    // 关闭 websocket
};
    }
    else {
// 浏览器不支持 WebSocket
alert("您的浏览器不支持 WebSocket!");
    }
}
```
