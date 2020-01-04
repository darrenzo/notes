# H5 Web Worker

- 参考[阮一峰 Web Worker 使用教程](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)
- 参考[MDN Web Worker 使用教程](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)
- 兼容性：IE和safari不支持**messageerror event**和**onmessageerror**，另外IE不支持**Transferable**对象。 移动端IOS的safari和Samsung不支持**messageerror event**和**onmessageerror**
- 概述：
  - JavaScript 语言采用的是单线程模型，随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程带来很大的不便，无法充分发挥计算机的计算能力
  - Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行, 在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程
- 优点：
  - 一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢
  - Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动（比如用户点击按钮、提交表单）打断，这样有利于随时响应主线程的通信
- 缺点：
  - Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，就应该关闭
- 注意点：
  - 兼容性：
    - IE 10+, Edge 12+, FireFox 3.5+, Chrome 4+, Safari 4+
    - `typeof(Worker)!=="undefined"`或 `window.Worker`检测浏览器是否支持
  - 同源限制（相同的协议，主机和端口号）
    - 分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源
  - DOM 限制
    - Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用document、window、parent这些对象。但是，Worker 线程可以使用**navigator**对象和**location**对象
      - 在worker线程里的navigator和location分别是WorkerNavigator和WorkerLocation的实例对象，并不完全是我们所熟悉的navigator和location
      - worker对象有以下几个全局属性和方法：
        - 最小化的**navigator**对象
          - 包括**onLine**、**appName**、**appVersion**、**userAgent**和**platform**属性
        - 只读的**location**属性
        - setTimeout()、setInterval()、clearTimeout()、clearInterval()方法
        - XMLHttpRequest构造函数
  - 通信联系
    - Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成
  - 脚本限制
    - Worker 线程不能执行**alert()**方法和**confirm()**方法，但可以使用 **XMLHttpRequest** 对象发出 **AJAX** 请求
  - 文件限制
    - Worker 线程无法读取本地文件，即不能打开本机的文件系统（**file://**），它所加载的脚本，必须来自网络
  - worker 线程必须等待主线程结束才开始
  - 专用worker仅仅能被首次生成它的脚本使用
    - [专用Worker基础实例](https://github.com/mdn/simple-web-worker)
  - 共享worker可以同时被多个脚本使用, 即使这些脚本正在被不同的window、iframe或者worker访问
    - [共享Worker基础实例](https://github.com/mdn/simple-shared-worker)
      - 示例中有2个HTML页面，每个页面所包含的javascript代码使用的是同一个worker
    - 如果共享worker可以被多个浏览上下文调用，所有这些浏览上下文必须属于同源
    - 在 Firefox中, 共享worker不能被私有和非私有window对象的document所共享

## API

### 主线程

- 浏览器原生提供**Worker()**构造函数，用来供主线程生成 Worker 线程

```js
// 生成专用worker
var myWorker = new Worker(jsUrl [, options]);
// jsUrl: 脚本的网址（必须遵守同源政策），且只能加载 JS 脚本，否则会报错
// options: 配置对象, 它的一个作用就是指定 Worker 的名称，用来区分多个 Worker 线程

// 主线程
var myWorker = new Worker('worker.js', { name : 'myWorker' });

// Worker 线程
self.name // myWorker
```

- **Worker()**构造函数返回一个 Worker 线程对象，用来供主线程操作 Worker
  - Worker.onerror
    - 指定 error 事件的监听函数
  - Worker.onmessage
    - 指定 message 事件的监听函数，发送过来的数据在Event.data属性中
  - Worker.onmessageerror
    - 指定 messageerror 事件的监听函数
    - 发送的数据无法序列化成字符串时，会触发这个事件
  - Worker.postMessage()
    - 向 Worker 线程发送消息
    - 要少用，每次使用会消耗大量资源
  - Worker.terminate()
    - 立即终止 Worker 线程

### Worker 线程

- Web Worker 有自己的全局对象，不是主线程的window，而是一个专门为 Worker 定制的全局对象
- 定义在window上面的对象和方法**不是全部**都可以使用
- Worker 线程的全局属性和方法（self可以换为this或者不写全局对象）
  - self.name
    - Worker 的名字。该属性只读，由构造函数指定
  - self.onmessage
    - 指定message事件的监听函数
  - self.onmessageerror
    - 指定 messageerror 事件的监听函数
    - 发送的数据无法序列化成字符串时，会触发这个事件
  - self.close()
    - 关闭 Worker 线程
  - self.postMessage()
    - 向产生这个 Worker 线程发送消息
  - self.importScripts()
    - 加载 JS 脚本

### 消息

- 拷贝而并非共享的那个值称为**消息**
- 使用 **postMessage()** 将消息传递给主线程或从主线程传送回来
- *postMessage()传递消息使用结构化拷贝算法
  - 结构化拷贝算法可以接收JSON数据以及一些JSON不能表示的数据——比如循环引用
  - 结构化拷贝算法支持的数据类型：
    - 所有的原始类型（除了symbols）
    - Boolean
    - String
    - Date
    - RegExp (lastIndex字段没有保存)
    - Blob
    - File
    - FileList
    - ArrayBuffer
    - ArrayBufferView
    - ImageBitmap
    - ImageData
    - Array
    - 普通对象
    - Map
    - Set

## 用法

### 主线程中的用法

```js
// 创建worker对象
// 由于 Worker 不能读取本地文件，所以这个脚本必须来自网络
// 如果下载没有成功（比如404错误），Worker 就会默默地失败
var worker = new Worker('work.js');

// 主线程调用worker.postMessage()方法，向 Worker 发消息
// 可以是各种数据类型，包括二进制数据
worker.postMessage('Hello World');
worker.postMessage({method: 'echo', args: ['Work']});

// 主线程通过worker.onmessage指定监听函数，接收子线程发回来的消息
worker.onmessage = function (event) {
  console.log('Received message ' + event.data);
  // 收到worker线程的消息后再发消息通知worker线程
  worker.postMessage('Work done!');
}

// 主线程关闭worker线程
worker.terminate();
```

### Worker线程中的用法

```js
// Worker 线程内部需要有一个监听函数，监听message事件
// self可以换成this或者不写
// self.onmessage同样可以指定监听函数
self.addEventListener('message', function (e) {
    // 收到主线程消息后，再发消息给主线程
    self.postMessage('You said: ' + e.data);
}, false);

// 在 Worker 内部关闭自身
self.close();
```

### Worker 加载脚本

- 浏览器加载并运行每一个列出的脚本
- 每个脚本中的全局对象都能够被 worker 使用
- 如果脚本无法加载，将抛出 **NETWORK_ERROR** 异常，接下来的代码也无法执行
  - 之前执行的代码(包括使用 window.setTimeout() 异步执行的代码)依然能够运行
  - importScripts() 之后的函数声明依然会被保留，因为它们始终会在其他代码之前运行
- 脚本的下载顺序不固定，但执行时会按照传入 importScripts() 中的文件名顺序进行
  - 这个过程是同步完成的
  - 直到所有脚本都下载并运行完毕，importScripts() 才会返回

```js
// Worker 内部加载1个或多个脚本
// 不传参则表示什么都不引入
importScripts('script1.js', 'script2.js', ...);
```

### 错误处理

- 主线程可以监听 Worker 是否发生错误
- Worker 内部也可以监听error事件

```js
worker.onerror(function (event) {
  console.log([
    // e.message: 可读性良好的错误消息
    // e.filename: 发生错误的脚本文件名
    // e.lineno: 发生错误时所在脚本文件的行号
    'ERROR: Line ', e.lineno, ' in ', e.filename, ': ', e.message
  ].join(''));
});

// 或者

worker.addEventListener('error', function (event) {
  // ...
});
```

## 数据通信

- 主线程与 Worker 之间的通信内容，可以是文本，也可以是对象
- 这种通信是拷贝关系，即是传值而不是传址，Worker 对通信内容的修改，不会影响到主线程
  - 浏览器内部的运行机制是，先将通信内容串行化，然后把串行化后的字符串发给 Worker，后者再将它还原

### 二进制数据的通信

- 主线程与 Worker 之间也可以交换二进制数据，比如 File、Blob、ArrayBuffer 等类型，也可以在线程之间发送

```js
// 主线程
var uInt8Array = new Uint8Array(new ArrayBuffer(10));
for (var i = 0; i < uInt8Array.length; ++i) {
  uInt8Array[i] = i * 2; // [0, 2, 4, 6, 8,...]
}
worker.postMessage(uInt8Array);

// Worker 线程
self.onmessage = function (e) {
  var uInt8Array = e.data;
  postMessage('Inside worker.js: uInt8Array.toString() = ' + uInt8Array.toString());
  postMessage('Inside worker.js: uInt8Array.byteLength = ' + uInt8Array.byteLength);
};
```

- 拷贝方式发送二进制数据，会造成性能问题
  - 主线程向 Worker 发送一个 500MB 文件，默认情况下浏览器会生成一个原文件的拷贝
  - **Transferable Objects**方法：为了解决性能问题，JavaScript 允许主线程把二进制数据直接转移给子线程，但是一旦转移，主线程就无法再使用这些二进制数据了，这是为了防止出现多个线程同时修改数据的麻烦局面
    - **Transferable Objects**方法使得主线程可以快速把数据交给 Worker，对于影像处理、声音处理、3D 运算等就非常方便了，不会产生性能负担

  ```js
  // Transferable Objects 格式
  worker.postMessage(arrayBuffer, [arrayBuffer]);

  // 例子
  var ab = new ArrayBuffer(1);
  worker.postMessage(ab, [ab]);
  ```

## 同页面的 Web Worker

- 通常情况下，Worker 载入的是一个单独的 JavaScript 脚本文件，但是也可以载入与主线程在同一个网页的代码

```html
<!-- 注意必须指定<script>标签的type属性是一个浏览器不认识的值，这里是app/worker -->
<!DOCTYPE html>
  <body>
    <!-- 目前没有一种「官方」的方法能够像 <script> 元素一样将 worker 的代码嵌入的网页中 -->
    <!-- 如果一个 <script> 元素没有 src 特性，并且它的 type 特性没有指定成一个可运行的 mime-type，那么它就会被认为是一个数据块元素，并且能够被 JavaScript 使用 -->
    <!-- 「数据块」是 HTML5 中一个十分常见的特性，它可以携带几乎任何文本类型的数据 -->
    <script id="worker" type="app/worker">
      addEventListener('message', function () {
        postMessage('some message');
      }, false);
    </script>
  </body>
</html>
```

```js
// 主线程读取这一段嵌入页面的脚本，用 Worker 来处理
var blob = new Blob([document.querySelector('#worker').textContent]);
var url = window.URL.createObjectURL(blob);
var worker = new Worker(url);

worker.onmessage = function (e) {
  // e.data === 'some message'
};
```

## Worker 线程完成轮询

- 浏览器需要轮询服务器状态，以便第一时间得知状态改变。这个工作可以放在 Worker 里面

```js
// Worker 每秒钟轮询一次数据，然后跟缓存做比较。如果不一致，就说明服务端有了新的变化，因此就要通知主线程

// 主线程
function createWorker(f) {
  var blob = new Blob(['(' + f.toString() +')()']);
  var url = window.URL.createObjectURL(blob);
  var worker = new Worker(url);
  return worker;
}

var pollingWorker = createWorker(function (e) {
  var cache;

  function compare(new, old) { ... };

  setInterval(function () {
    fetch('/my-api-endpoint').then(function (res) {
      var data = res.json();

      if (!compare(data, cache)) {
        cache = data;
        self.postMessage(data);
      }
    })
  }, 1000)
});

pollingWorker.onmessage = function () {
  // render data
}

pollingWorker.postMessage('init');
```

## Worker 新建 Worker

- Worker 线程内部还能再新建 Worker 线程
  - 这就是所谓的subworker它们必须托管在同源的父页面内。而且，subworker 解析 URI 时会相对于父 worker 的地址而不是自身页面的地址。这使得 worker 更容易记录它们之间的依赖关系
- 目前只有 **Firefox 浏览器**支持
- 例如：将一个计算密集的任务，分配到10个 Worker

```js
// 主线程
var worker = new Worker('worker.js');
worker.onmessage = function (event) {
  document.getElementById('result').textContent = event.data;
};
```

```js
// worker.js
// settings
var num_workers = 10;
var items_per_worker = 1000000;

// start the workers
var result = 0;
var pending_workers = num_workers;
for (var i = 0; i < num_workers; i += 1) {
  var worker = new Worker('core.js');
  worker.postMessage(i * items_per_worker);
  worker.postMessage((i + 1) * items_per_worker);
  worker.onmessage = storeResult;
}

// handle the results
function storeResult(event) {
  result += event.data;
  pending_workers -= 1;
  if (pending_workers <= 0)
    postMessage(result); // finished!
}
```

```js
// core.js
var start;
onmessage = getStart;
function getStart(event) {
  start = event.data;
  onmessage = getEnd;
}

var end;
function getEnd(event) {
  end = event.data;
  onmessage = null;
  work();
}

function work() {
  var result = 0;
  for (var i = start; i < end; i += 1) {
    // perform some complex calculation here
    result += 1;
  }
  postMessage(result);
  close();
}
```

## 共享Worker

```js
var myWorker = new SharedWorker('worker.js');
```

- 一个非常大的区别在于，与一个共享worker通信必须通过端口对象
  - 一个确切的打开的端口供脚本与worker通信（在专用worker中这一部分是隐式进行的）
- 在传递消息之前，端口连接必须被显式的打开，打开方式是使用**onmessage**事件处理函数或者**start()**方法
  - start()方法的调用只在一种情况下需要，那就是消息事件被addEventListener()方法使用
    - **onmessage**事件处理函数和**start()**方法都会打开端口连接，没必要同时使用
  - 在使用start()方法打开端口连接时，如果父级线程和worker线程需要双向通信，那么它们都需要调用start()方法

```js
myWorker.port.start();  // 父级线程中的调用
port.start(); // worker线程中的调用, 假设port变量代表一个端口
```

### 消息接收和发送

- **postMessage()** 方法必须被端口对象调用

```js
// 主线程
myWorker.port.postMessage([squareNumber.value,squareNumber.value]);
myWorker.port.onmessage = function(e) {
  result2.textContent = e.data;
}

// Worker线程
// 当一个端口连接被创建时（例如：在父级线程中，设置onmessage事件处理函数，或者显式调用start()方法时），使用onconnect事件处理函数来执行代码
onconnect = function(e) {
  // 使用事件的ports属性来获取端口并存储在变量中
  var port = e.ports[0];
  // 在worker线程中设置此消息处理函数也会隐式的打开与主线程的端口连接
  port.onmessage = function(e) {
    var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
    port.postMessage(workerResult);
  }
}
```

## 其他类型的Workers

- [ServiceWorkers(服务worker)](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API)
  - 一般作为web应用程序、浏览器和网络（如果可用）之间的代理服务器
  - 它们旨在（除开其他方面）创建有效的离线体验，拦截网络请求，以及根据网络是否可用采取合适的行动并更新驻留在服务器上的资源
  - 他们还将允许访问推送通知和后台同步API
- [Chrome Workers](https://developer.mozilla.org/zh-CN/docs/Web/API/ChromeWorker)
  - 仅适用于firefox的worker
  - 如果你正在开发附加组件，希望在扩展程序中使用worker且有在你的worker中访问  js-ctypes 的权限，你可以使用Chrome Workers
- [Audio Workers(音频worker)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API#Audio_Workers)
  - 使得在web worker上下文中直接完成脚本化音频处理成为可能

## 线程安全

- Worker接口会生成真正的操作系统级别的线程,但对于 web worker 来说，与其他线程的通信点会被很小心的控制，这意味着你很难引起并发问题
- 没有办法去访问非线程安全的组件或者是 DOM，此外你还需要通过序列化对象来与线程交互特定的数据

## 内容安全策略（CSP）

- 有别于创建它的document对象，worker有它自己的执行上下文, 所以一般来说，worker并不受限于创建它的document（或者父级worker）的内容安全策略
- 为了给worker指定内容安全策略，必须为发送worker代码的请求本身加上一个[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
- 特殊情况：worker脚本的源如果是一个全局性的唯一的标识符（例如，它的URL指定了数据模式或者blob），worker则会继承创建它的document或者worker的CSP
