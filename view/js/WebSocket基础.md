WebSocket 协议在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于[服务器推送技术](https://en.wikipedia.org/wiki/Push_technology)的一种。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-95d1447373aa5f11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其他特点包括：
1. 建立在 TCP 协议之上，服务器端的实现比较容易。

2. 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，握手阶段可以通过 HTTP 代理。
3. 数据格式比较轻量，性能开销小，通信高效。
4. 可以发送文本，也可以发送二进制数据。
5. 没有同源限制，客户端可以与任意服务器通信。
6. 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。
`ws://example.com:80/some/path`

![image.png](https://upload-images.jianshu.io/upload_images/18030682-644183492538ef57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 客户端的简单示例
```javascript
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};
```

### 客户端的 API

**4.1 WebSocket 构造函数**

WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例。

```javascript
 var ws = new WebSocket('ws://localhost:8080');
```
执行上面语句之后，客户端就会与服务器进行连接。

实例对象的所有属性和方法清单，参见[这里](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)。

**4.2 webSocket.readyState**

`readyState`属性返回实例对象的当前状态，共有四种。

> *   CONNECTING：值为0，表示正在连接。
> *   OPEN：值为1，表示连接成功，可以通信了。
> *   CLOSING：值为2，表示连接正在关闭。
> *   CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

下面是一个示例。

``` javascript
 switch (ws.readyState) {
   case WebSocket.CONNECTING:
     // do something
     break;
   case WebSocket.OPEN:
     // do something
     break;
   case WebSocket.CLOSING:
     // do something
     break;
   case WebSocket.CLOSED:
     // do something
     break;
   default:
     // this never happens
     break;
 }

```

**4.3 webSocket.onopen**

实例对象的`onopen`属性，用于指定连接成功后的回调函数。

 ```javascript
 ws.onopen = function () {
   ws.send('Hello Server!');
 }
 
 ```

如果要指定多个回调函数，可以使用`addEventListener`方法。

 ```javascript
 
 ws.addEventListener('open', function (event) {
   ws.send('Hello Server!');
 });
 
 ```

**4.4 webSocket.onclose**

实例对象的`onclose`属性，用于指定连接关闭后的回调函数。

 ``` javascript
 ws.onclose = function(event) {
   var code = event.code;
   var reason = event.reason;
   var wasClean = event.wasClean;
   // handle close event
 };

 ws.addEventListener("close", function(event) {
   var code = event.code;
   var reason = event.reason;
   var wasClean = event.wasClean;
   // handle close event
 });
 
 ```

**4.5 webSocket.onmessage**

实例对象的`onmessage`属性，用于指定收到服务器数据后的回调函数。

 ```javascript
 ws.onmessage = function(event) {
   var data = event.data;
   // 处理数据
 };
 
 ws.addEventListener("message", function(event) {
   var data = event.data;
   // 处理数据
 });
 ```

注意，服务器数据可能是文本，也可能是二进制数据（`blob`对象或`Arraybuffer`对象）。

 ```javascript
 
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

除了动态判断收到的数据类型，也可以使用`binaryType`属性，显式指定收到的二进制数据类型。

 ```javascript
 
 // 收到的是 blob 数据
 ws.binaryType = "blob";
 ws.onmessage = function(e) {
   console.log(e.data.size);
 };
 
 // 收到的是 ArrayBuffer 数据
 ws.binaryType = "arraybuffer";
 ws.onmessage = function(e) {
   console.log(e.data.byteLength);
 };
 
 ```

**4.6 webSocket.send()**

实例对象的`send()`方法用于向服务器发送数据。

发送文本的例子。

 ```javascript
 ws.send('your message');
 ```

发送 Blob 对象的例子。

 ``` javascript
 var file = document
   .querySelector('input[type="file"]')
   .files[0];
 ws.send(file);
 ```

发送 ArrayBuffer 对象的例子。
```javascript
 // Sending canvas ImageData as ArrayBuffer
 var img = canvas_context.getImageData(0, 0, 400, 320);
 var binary = new Uint8Array(img.data.length);
 for (var i = 0; i < img.data.length; i++) {
   binary[i] = img.data[i];
 }
 ws.send(binary.buffer);

```

**4.7 webSocket.bufferedAmount**

实例对象的`bufferedAmount`属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。
 ```javascript
 
 var data = new ArrayBuffer(10000000);
 socket.send(data);
 
 if (socket.bufferedAmount === 0) {
   // 发送完毕
 } else {
   // 发送还没结束
 }
 
 ```

**4.8 webSocket.onerror**

实例对象的`onerror`属性，用于指定报错时的回调函数。

 ```javascript
 
 socket.onerror = function(event) {
   // handle error event
 };
 
 socket.addEventListener("error", function(event) {
   // handle error event
 });
 ```

(本文来自阮一峰大神的[WebSocket教程]([http://www.ruanyifeng.com/blog/2017/05/websocket.html](http://www.ruanyifeng.com/blog/2017/05/websocket.html)
))