# H5 Web Socket

- 参考[菜鸟教程](https://www.runoob.com/html/html5-websocket.html) 和 [廖雪峰教程](https://www.liaoxuefeng.com/wiki/1022910821149312/1103303693824096)
- WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议
  - 本质上是一个基于 TCP 的协议
  - WebSocket并不是全新的协议，而是利用了HTTP协议来建立连接
  - WebSocket连接必须由浏览器发起，因为请求协议是一个标准的HTTP请求
- WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据
- 在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输
- WebSocket 协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯

## 传统的轮询或者Comet方法

- 轮询是指浏览器通过JavaScript启动一个定时器，然后以固定的间隔给服务器发请求，询问服务器有没有新消息
  - 缺点： 实时性不够；频繁的请求会给服务器带来极大的压力
- Comet本质上也是轮询，就是在没有消息的情况下，服务器先拖一段时间，等到有消息了再回复
  - 这个机制暂时地解决了实时性问题，但是它带来了新的问题：以多线程模式运行的服务器会让大部分线程大部分时间都处于挂起状态，极大地浪费服务器资源
  - 另外，一个HTTP连接在长时间没有数据传输的情况下，链路上的任何一个网关都可能关闭这个连接，而网关是我们不可控的，这就要求Comet连接必须定期发一些ping数据表示连接“正常工作”

## 实现原理

- 为了建立一个 WebSocket 连接， 浏览器通过 JavaScript 向服务器发出建立 WebSocket 连接的 HTTP 请求，这个请求和通常的 HTTP 请求不同，包含了一些附加头信息，其中附加头信息"Upgrade: WebSocket"表明这是一个申请协议升级的 HTTP 请求，服务器端解析这些附加的头信息然后产生应答信息返回给客户端，客户端和服务器端的 WebSocket 连接就建立起来了，双方就可以通过这个连接通道自由的传递信息，并且这个连接会持续存在直到客户端或者服务器端的某一方主动的关闭连接
- 当你获取 Web Socket 连接后，你可以通过 send() 方法来向服务器发送数据，并通过 onmessage 事件来接收服务器返回的数据
- 消息有两种，一种是文本，一种是二进制数据。通常，我们可以发送JSON格式的文本，这样，在浏览器处理起来就十分容易
- 实际上HTTP协议是建立在TCP协议之上的，TCP协议本身就实现了全双工通信，但是HTTP协议的请求－应答机制限制了全双工通信。WebSocket连接建立以后，其实只是简单规定了一下：接下来，咱们通信就不使用HTTP协议了，直接互相发数据吧

## 注意事项

- Websocket 使用 ws 或 wss 的统一资源标志符，类似于 HTTPS，其中 wss 表示在 TLS 之上的 Websocket
  - 如： ws://example.com/wsapi     wss://secure.example.com/
  - GET请求的地址不是类似/path/，而是以ws://开头的地址
  - 安全的 WebSocket 连接机制和HTTPS类似。首先，浏览器用wss://xxx创建WebSocket连接时，会先通过HTTPS创建安全的连接，然后，该HTTPS连接升级为WebSocket连接，底层通信走的仍然是安全的SSL/TLS协议
- Websocket 使用和 HTTP 相同的 TCP 端口，可以绕过大多数防火墙的限制
  - 默认情况下，Websocket 协议使用 80 端口；运行在 TLS 之上时，默认使用 443 端口

## 浏览器兼容性

- Chrome
- Firefox
- IE >= 10
- Sarafi >= 6
- Android >= 4.4
- iOS >= 8

## 创建 WebSocket 对象

```js
var Socket = new WebSocket(url[, protocol]);
```

## WebSocket 属性

- Socket.binaryType
  - 使用二进制的数据类型连接
- Socket.readyState
  - 只读属性, 表示连接状态
  - 可能的值：
    - 0 - 表示连接尚未建立
    - 1 - 表示连接已建立，可以进行通信
    - 2 - 表示连接正在进行关闭
    - 3 - 表示连接已经关闭或者连接不能打开
- Socket.extensions
  - 只读属性，服务器选择的扩展
- Socket.protocol
  - 只读属性，服务器选择的下属协议
- Socket.bufferedAmount
  - 只读属性，表示已被 send() 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数
- Socket.url
  - 只读属性，WebSocket 的绝对路径

## WebSocket 事件

- Socket.onopen
  - 连接建立时触发
- Socket.onmessage
  - 客户端接收服务端数据时触发
- Socket.onerror
  - 通信发生错误时触发
- Socket.onclose
  - 连接关闭时触发

## WebSocket 方法

- Socket.send(data)
  - 使用连接发送数据
  - data: 用于传输至服务器的数据, 它必须是以下类型之一
    - USVString: 文本字符串。字符串将以 UTF-8 格式添加到缓冲区，并且 bufferedAmount 将加上该字符串以 UTF-8 格式编码时的字节数的值
    - ArrayBuffer: 可以使用一有类型的数组对象发送底层二进制数据；其二进制数据内存将被缓存于缓冲区，bufferedAmount 将加上所需字节数的值
    - Blob: Blob 类型将队列 blob 中的原始数据以二进制传输。 bufferedAmount 将加上原始数据的字节数的值
    - ArrayBufferView: 可以以二进制帧的形式发送任何 JavaScript 类数组对象 ；其二进制数据内容将被队列于缓冲区中。值 bufferedAmount 将加上必要字节数的值
  - 异常
    - INVALID_STATE_ERR： 当前连接未处于 OPEN 状态
    - SYNTAX_ERR：数据是一个包含未配对代理(unpaired surrogates)的字符串
- Socket.close([code[, reason]])
  - 关闭 WebSocket  连接或连接尝试（如果有的话）,如果连接已经关闭,则此方法不执行任何操作
  - code: 一个数字状态码，它解释了连接关闭的原因。如果没有传这个参数，默认使用1005
    - [允许的状态码](https://developer.mozilla.org/en-US/docs/Web/API/CloseEvent#Status_codes)
  - reason: 一个人类可读的字符串，它解释了连接关闭的原因。这个UTF-8编码的字符串不能超过123个字节
- 抛出的异常
  - INVALID_ACCESS_ERR：一个无效的code
  - SYNTAX_ERR： reason 字符串太长（超过123字节）

## 写法

```js
// 浏览器端
function WebSocketTest() {

    if ("WebSocket" in window) {

        alert("您的浏览器支持 WebSocket!");

        // 打开一个 web socket
        var ws = new WebSocket("ws://localhost:9998/echo");

        ws.onopen = function() {
            // Web Socket 已连接上，使用 send() 方法发送数据
            ws.send("发送数据");
            alert("数据发送中...");
        };

        ws.onmessage = function (evt) {
            var received_msg = evt.data;
            alert("数据已接收...");
        };

        ws.onclose = function() {
            // 关闭 websocket
            alert("连接已关闭...");
        };
    } else {
        // 浏览器不支持 WebSocket
        alert("您的浏览器不支持 WebSocket!");
    }
}
WebSocketTest(); // 执行则会尝试建立连接
```

## demo

- 创建一个支持 WebSocket 的服务
  - `git clone https://github.com/google/pywebsocket.git` 下载 mod_pywebsocket
- mod_pywebsocket 需要 python 环境支持
- mod_pywebsocket 是一个 Apache HTTP 的 Web Socket 扩展
- 安装步骤：
  - 进入 pywebsocket 目录
  
  ```shell
    python setup.py build
    sudo python setup.py install
    # 查看文档说明
    pydoc mod_pywebsocket
  ```

- 开启服务
  - 在 pywebsocket/mod_pywebsocket 目录下执行以下命令
  
  ```shell
  sudo python standalone.py -p 9998 -w ../example/
  # 以上命令会开启一个端口号为 9998 的服务，使用 -w 来设置处理程序 echo_wsh.py 所在的目录
  ```

- 查看效果

```html
<!-- 在浏览器运行 -->
<!-- 在停止服务后，会弹出 "连接已关闭..." -->
<!DOCTYPE HTML>
<html>
   <head>
    <meta charset="utf-8">
    <title>aaaa.com</title>
   </head>
   <body>

    <div id="sse">
       <a href="javascript:WebSocketTest()">运行 WebSocket</a>
    </div>

   </body>
</html>
```

## 典型的 Websocket 握手请求

```shell
# 客户端请求
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13

# 服务器回应
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```

- Connection 必须设置 Upgrade，表示客户端希望连接升级
- Upgrade 字段必须设置 Websocket，表示希望升级到 Websocket 协议
- Sec-WebSocket-Key 是随机的字符串，服务器端会用这些数据来构造出一个 SHA-1 的信息摘要
  - 把 “Sec-WebSocket-Key” 加上一个特殊字符串 “258EAFA5-E914-47DA-95CA-C5AB0DC85B11”，然后计算 SHA-1 摘要，之后进行 BASE-64 编码，将结果做为 “Sec-WebSocket-Accept” 头的值，返回给客户端
  - 用于标识这个连接，并非用于加密数据，这样可以尽量避免普通 HTTP 请求被误认为 Websocket 协议
- Sec-WebSocket-Version 表示支持的 Websocket 版本
  - RFC6455 要求使用的版本是 13，之前草案的版本均应当弃用
- Origin 字段是可选的，通常用来表示在浏览器中发起此 Websocket 连接所在的页面
  - 类似于 Referer, 但是，与 Referer 不同的是，Origin 只包含了协议和主机名称
- 其他一些定义在 HTTP 协议中的字段，如 Cookie 等，也可以在 Websocket 中使用
- 该响应代码101表示本次连接的HTTP协议即将被更改，更改后的协议就是Upgrade: websocket指定的WebSocket协议
- 版本号和子协议规定了双方能理解的数据格式，以及是否支持压缩等等。如果仅使用WebSocket的API，就不需要关心这些
- 由于WebSocket是一个协议，服务器具体怎么实现，取决于所用编程语言和框架本身。Node.js本身支持的协议包括TCP协议和HTTP协议，要支持WebSocket协议，需要对Node.js提供的HTTPServer做额外的开发（npm有许多实现的包）
- 在服务器方面，网上都有不同对 websocket 支持的服务器
  - php - `http://code.google.com/p/phpwebsocket/`
  - jetty - `http://jetty.codehaus.org/jetty/`（版本7开始支持websocket）
  - netty - `http://www.jboss.org/netty`
  - ruby - `http://github.com/gimite/web-socket-ruby`
  - Kaazing - `https://web.archive.org/web/20100923224709/http://www.kaazing.org/confluence/display/KAAZING/Home`
  - Tomcat - `http://tomcat.apache.org/`（7.0.27支持websocket，建议用tomcat8，7.0.27中的接口已经过时）
  - WebLogic - `http://www.oracle.com/us/products/middleware/cloud-app-foundation/weblogic/overview/index.html`（12.1.2开始支持）
  - node.js - `https://github.com/Worlize/WebSocket-Node`
  - node.js - `http://socket.io`
  - nginx - `http://nginx.com/`
  - mojolicious - `http://mojolicio.us/`
  - python - `https://github.com/abourget/gevent-socketio`
  - Django - `https://github.com/stephenmcd/django-socketio`
  - erlang - `https://github.com/ninenines/cowboy.git`

## webSocket 与 socket 的区别

- Socket 是传输控制层协议，WebSocket 是应用层协议
  - 软件通信有七层结构，下三层结构定义了应用程序的功能（偏向于数据通信），上三层主要面向通过网络的端到端，点到点的数据流（更偏向于数据处理），中间的传输层则是连接上三层与下三层之间的桥梁，每一层都做不同的工作，上层协议依赖于下层协议
    - 网络七层协议（OSI模型）：7 应用层 6 表示层 5 会话层 4 传输层 3 网络层 2 数据链路层 1 物理层
  - Socket 其实并不是一个协议，是应用层与 TCP/IP 协议通信的中间软件抽象层，它是一组接口
  - 当两台主机通信时，让 Socket 去组织数据，以符合指定的协议
  - TCP 连接则更依靠于底层的 IP 协议，IP 协议的连接则依赖于链路层等更低层次

## ws 包的使用

- 要使用 WebSocket，关键在于服务器端支持
- 在Node.js中，使用最广泛的 WebSocket 模块是 **ws**

- 服务器端代码

```js
// app.js中编写 WebSocket
const WebSocket = require('ws');

// 引用Server类:
const WebSocketServer = WebSocket.Server;

// 实例化:
const wsServer = new WebSocketServer({
    port: 3000 // 值为端口号或者一个 http.Server 端口对象
});

// 如果有WebSocket请求接入，wsServer对象可以响应connection事件来处理这个WebSocket
// WebSocketServer会首先判断请求是不是WS请求，如果是，它将处理该请求，如果不是，该请求将有其他接口处理
wsServer.on('connection', function (ws) {
    // ws 是这个WebSocket连接
    // 对于每个WebSocket连接，我们都要对它绑定某些事件方法来处理不同的事件
    console.log(`[SERVER] connection()`);
    ws.on('message', function (message) {
        console.log(`[SERVER] Received: ${message}`);
        ws.send(`ECHO: ${message}`, (err) => {
            if (err) {
                console.log(`[SERVER] error: ${err}`);
            }
        });
    })
});

// 可以直接用ws模块提供的WebSocket来充当客户端
// ws模块既包含了服务器端，又包含了客户端
// ws的WebSocket就表示客户端，它其实就是WebSocketServer响应connection事件时回调函数传入的变量ws的类型
let ws = new WebSocket('ws://localhost:3000/test');

// 打开WebSocket连接后立刻发送一条消息:
ws.on('open', function () {
    console.log(`[CLIENT] open()`);
    ws.send('Hello!');
});

// 响应收到的消息:
ws.on('message', function (message) {
    console.log(`[CLIENT] Received: ${message}`);
}
// 在Node环境下，ws模块的客户端可以用于测试服务器端代码，否则，每次都必须在浏览器执行JavaScript代码
```

- 也可以在浏览器端写 js

```js
var ws = new WebSocket('ws://localhost:3000/test');
// 响应onmessage事件:
ws.onmessage = function(msg) {
    console.log(msg);
    // MessageEvent {isTrusted: true, data: "ECHO: Hello!", origin: "ws://localhost:3000", lastEventId: "", source: null…}
};
// 给服务器发送一个字符串:
ws.send('Hello!');
```

## 同源策略

- WebSocket协议本身不要求同源策略（Same-origin Policy），也就是某个地址为 `http://a.com` 的网页可以通过 WebSocket 连接到 `ws://b.com`
  - 浏览器会发送 Origin 的 HTTP 头给服务器，服务器可以根据 Origin 拒绝这个 WebSocket 请求。所以，是否要求同源要看服务器端如何检查

## 路由

- 服务器在响应 connection 事件时并未检查请求的路径，因此，在客户端打开 `ws://localhost:3000/any/path` 可以写任意的路径
  - 实际应用中还需要根据不同的路径实现不同的功能

## 聊天室案例（webSocket + koa）

- [廖雪峰教程](https://www.liaoxuefeng.com/wiki/1022910821149312/1103332447876608)
- 通过cookies验证用户身份
  - wsServer 的 connection 事件中回调中 ws.upgradeReq 是一个 request 对象，可以用作 user 的存取

  ```js
  // 识别用户身份的逻辑
  function parseUser(obj) {
      if (!obj) {
          return;
      }
      console.log('try parse: ' + obj);
      let s = '';
      if (typeof obj === 'string') {
          s = obj;
      } else if (obj.headers) {
          let cookies = new Cookies(obj, null);
          s = cookies.get('name');
      }
      if (s) {
          try {
              let user = JSON.parse(Buffer.from(s, 'base64').toString());
              console.log(`User: ${user.name}, ID: ${user.id}`);
              return user;
          } catch (e) {
              // ignore
          }
      }
  }
  ```

- wsServer.clients 是服务器当前连接中的所有 WebSocket 对象
