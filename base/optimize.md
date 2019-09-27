# optimize

## 性能分析工具 ？？？？？

- dynaTrace

## 输入网址到页面呈现的过程

- 预处理——>DNS解析——>建立连接——>发起请求——>等待响应——>接受数据——>处理元素——>布局渲染

## 性能优化

![性能优化操作](/img/optimize.webp)

- PC优化手段在移动端同样适用，移动端有3秒渲染完成首屏制表（首屏加载3秒完成或适用loading）
  - 基于联通3G网络平均338KB/s, 首屏资源不应超过1014KB
- 移动端因手机配置原因，除加载外渲染速度也是优化重点

### 页面级优化

#### 减少 HTTP 请求数

- 主要方式有脚本合并、图片合并、文件压缩、避免重定向、使用浏览器缓存、减少cookie传输
- 成本（时间和资源）：
  - 一个完整的请求都需要经过 DNS寻址、与服务器建立连接、发送数据、等待服务器响应、接收数据这样的过程
  - 每个请求都需要携带数据，因此每个请求都需要占用带宽
  - 浏览器进行并发请求的请求数是有上限的，因此请求数多了以后，浏览器需要分批进行请求，会增加用户的等待时间
- 解决途径
  - 从设计实现层面简化页面
    - 保持页面简洁、减少资源的使用是最直接的
    - HTML页面渲染时请求资源相同的只会请求一次，重复的都在缓存中取
    - ajax请求，如果做了缓存处理，则重复的请求也会在缓存里取
  - DNS缓存
    - 由于浏览器会在DNS解析步骤中消耗一定的时间，所以，对于一些高访问量网站来说，做好DNS的缓存工作，就会一定程度上提升网站效率
  - CDN部署与缓存
    - CDN作为静态资源文件的分发网络，本身就已经提升了网站静态资源的获取速度，加快网站的加载速度，同时也给静态资源做好缓存工作，有效的利用已缓存的静态资源，加快获取速度
  - HTTP缓存
    - 给资源设定缓存时间，防止在有效的缓存时间内对资源进行重复的下载，从而提升整体网页的加载速度
    - 第二次访问(即浏览器已缓存之后访问), 已缓存的资源不再请求
      - 使用强缓存静态资源文件
        - 强缓存就是利用http头中的**cache-control**和**expires**属性
      - 协商缓存 **ETag/If-None-Match;Last-Modified/If-Moidfied-Since**
      - 直接F5刷新页面的话效果是不一样的：请求数还是一样，不过被缓存资源的请求服务器是304响应，只有Header没有Body ，可以节省带宽
      - 例如：很少变化的图片资源可以直接通过**HTTP Header**中的**Expires**设置一个很长的过期头
      - 例如：变化不频繁而又可能会变的资源可以使用**Last-Modifed**来做请求验证
    - 针对移动端的缓存优化
      - 使用长cache
        - 可以使得移动端的部分资源设定长期缓存，这样可以保证资源不用向服务器发送请求，来比较资源是否更新，从而避免304的情况
      - 首屏优化
        - 2s时间是用户的最佳体验，一旦超出这个时间，将会导致用户的流失
      - 不滥用web字体
        - web字体的好处就是，可以代替某些图片资源，但是，在移动端过多的web字体的使用，会导致页面资源加载的繁重
  - 资源合并与压缩
    - 以适当的程度合并相应的外部资源（还要考虑缓存的因素），并进行压缩
    - 图片尽量使用png等图片格式，减少矢量图、高清图等的使用
    - 减少css标签、javascript标签
      - 一般页面大概适合2-3个script文件进行加载，css同样，现有的框架也很好的实现了资源的合并
    - gzip压缩
      - gzip压缩是http协议上的gzip压缩编码技术
      - 一般会在www服务器上配置服务，然后将压缩过的文件传输到客户端，由客户端进行内容的显示
        - 例如：nginx服务器就可以进行gzip配置，默认会对text/html的文件进行压缩
      - gzip_http_version默认是1.1的，即只有http/1.1的协议才能进行gzip压缩
        - 如果使用了proxy_pass进行反向代理，那么nginx和后端的upstream server之间是用HTTP/1.0协议通信的。如果使用nginx通过反向代理做Cache Server，前端的nginx没有开启gzip，且后端的nginx上未设置gzip_http_version为1.0，那么Cache的url将不会被gzip压缩
        - 例如：
          - 请求头：
            - Accept-Encoding: gzip, deflate
          - 响应头
            - Content-Encoding: gzip
  - 避免重定向
    - 重定向会增加http请求数，但必要的重定向有利于提高用户体验
    - 定义链接URL时使用最完整的、最直接的地址
  - 减少cookie传输
    - ookie包含在每次请求和响应中，太大的cookie会严重影响数据传输，因此尽量减少cookie中传输的数据量
  - css 精灵图
  - image src的base64写法
    - 一般只用在css文件里，而且很少用
    - 如果是嵌入页面的话换来的是增大了页面的体积，而且无法利用浏览器缓存
  - Images懒加载
  - 首屏进度条的显示
    - 往往对于首屏优化后的数据量并不满意的话，同时也不能进一步缩短首屏包的长度了，就可以使用进度条的方式，来提醒用户进行等待
  - 大量数据的运算 ？？？？？
    - JS本身的单线程就限制了它并不能计算大量的数据，往往会造成页面的卡顿
    - 业务中有些复杂的UI需要去运行大量的运算，所以，**webWorker**的使用是至关重要的

#### 异步请求

- JSON交互
  - JSON的数据格式轻巧，结构简单，往往可以大大优化前后端的数据通信
- 常用数据的缓存
  - 可以将一些用户的基本信息等常用的信息做一个缓存，这样可以保证ajax请求的减少
  - HTML5新增的storage的内容，也不用怕cookie暴露，引起的信息泄漏问题
- 数据埋点和统计
  - 一般指网站统计数据用的跟踪代码
  - 埋点技术一般分为：
    - 代码埋点
      - 在你需要统计数据的地方植入N行代码，统计用户的关键行为
      - 优点：
        - 使用者控制精准，可以非常精确地选择什么时候发送数据
        - 使用者可以比较方便地设置自定义属性、自定义事件，传递比较丰富的数据到服务端
      - 缺点：
        - 埋点代价比较大，每一个控件的埋点都需要添加相应的代码
        - 更新代价比较大，每一次更新，都需要更新埋点方案，然后通过各个应用市场进行分发，而且有的用户还不一定更新，这样你就获取不到这批用户数
    - 可视化埋点
      - 把核心代码和配置、资源分开，每次用户启动app的时候通过网络更新配置和资源，通过界面配置哪些控件的操作数据需要收集
      - 优点：
        - 可视化埋点解决了代码埋点代价大和更新代价大两个问题
      - 缺点
        - 可视化埋点能够覆盖的功能有限，目前并不是所有的控件操作都可以通过这种方案进行定制
        - 可视化埋点只能收集到你埋点以后的数据，如果你想对某个按钮进行点击分析，则只能分析增加可视化埋点以后的数据，之前的数据你收集不到
    - 无埋点（即全埋点）
      - 无埋点对页面所有元素进行埋点，先尽可能收集所有控件的操作数据，然后再通过界面配置哪些数据需要在系统里面进行分析
      - 优点：
        - 无埋点在部署SDK的时候数据就一直在收集
        - 可以知道页面每个元素被点击的概率，所以对点击概率比较大的元素可以进行深入分析
  
#### 资源预加载

- Pre-fetching：指的是一种提示浏览器预先加载用户之后可能会使用到的资源的方法
- 有以下方法：
  - preload 必须和as属性合用
    - 参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content)
    - 推荐使用(IE不支持，Firefox仅支持可缓存资源的预加载)，还可以配合type属性来选择性加载
    - `<link rel="preload"  href="/some_other_resource.js" as="script">`
    - 使用 **as** 来指定将要预加载的内容的类型，将使得浏览器能够
      - 更精确地优化资源加载优先级
      - 匹配未来的加载需求，在适当的情况下，重复利用同一资源
      - 为资源应用正确的内容安全策略
      - 为资源设置正确的 **Accept** 请求头
    - **as** 主要的取值
      - audio: 音频文件
      - document: 一个将要被嵌入到`<frame>`或`<iframe>`内部的HTML文档
      - embed: 一个将要被嵌入到`<embed>`元素内部的资源
      - fetch: 那些将要通过fetch和XHR请求来获取的资源，比如一个ArrayBuffer或JSON文件
      - font: 字体文件
      - image: 图片文件
      - object: 一个将会被嵌入到`<embed>`元素内的文件
      - script: JavaScript文件
      - style: 样式表
      - track: WebVTT文件
      - worker: 一个JavaScript的**web worker**或**shared worker**
      - video: 视频文件
    - 预加载的写法：

      ```html
      <head>
        <meta charset="utf-8">
        <title>JS and CSS preload example</title>
        <!-- 预加载 -->
        <link rel="preload" href="style.css" as="style">
        <link rel="preload" href="main.js" as="script">
        <!-- 开始使用预加载好的css -->
        <link rel="stylesheet" href="style.css">
      </head>

      <body>
        <h1>bouncing balls</h1>
        <canvas></canvas>
        <!-- 开始使用预加载好的js -->
        <script src="main.js"></script>
      </body>
      ```

      - type属性的使用

      ```html
      <!-- 预加载机制与渐进式增强结合 -->
      <head>
        <meta charset="utf-8">
        <title>Video preload example</title>
        <!-- 预加载mp4 -->
        <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
      </head>
      <body>
        <video controls>
          <!-- 浏览器支持MP4，则使用预加载好的MP4 -->
          <source src="sintel-short.mp4" type="video/mp4">
          <!-- 不支持MP4,则开始加载webm -->
          <source src="sintel-short.webm" type="video/webm">
          <p>12313131</p>
        </video>
      </body>
      ```

      - 跨域获取
        - 如果你已经有了一个可以正确工作的CORS设置，那么你也可以同样成功的预加载那些跨域资源，只需要你在`<link>`元素中设置好**crossorigin**属性即可
        - 字体文件即使是非跨域的情况下，也需要应用**crossorigin**属性(以匿名模式)
        - 字体在随着页面渲染完成的时候即可使用，减少了FOUT (无样式字体闪烁，flash of unstyled text)问题

      ```html
      <head>
        <meta charset="utf-8">
        <title>Web font example</title>

        <link rel="preload" href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
        <link rel="preload" href="fonts/cicle_fina-webfont.woff2" as="font" type="font/woff2" crossorigin="anonymous">
        <link rel="preload" href="fonts/cicle_fina-webfont.woff" as="font" type="font/woff" crossorigin="anonymous">
        <link rel="preload" href="fonts/cicle_fina-webfont.ttf" as="font" type="font/ttf" crossorigin="anonymous">
        <link rel="preload" href="fonts/cicle_fina-webfont.svg" as="font" type="image/svg+xml" crossorigin="anonymous">

        <link href="style.css" rel="stylesheet" type="text/css">
      </head>
      ```

      - 配合**media**属性实现响应式预加载
        - `<link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">`
      - 脚本化与预加载
        - 当你需要预加载一个脚本，但需要推迟到需要的时候才令其执行时，这种方式会特别有用
          - IE可以直接使用script标签的**defer**属性实现

      ```js
        // 浏览器将预加载这个JavaScript文件，但并不实际执行它
        let preloadLink = document.createElement("link");
        preloadLink.href = "myscript.js";
        preloadLink.rel = "preload";
        preloadLink.as = "script";
        document.head.appendChild(preloadLink);

        // 执行预加载好的脚本
        let preloadedScript = document.createElement("script");
        preloadedScript.src = "myscript.js";
        document.body.appendChild(preloadedScript);
      ```

  - prefetch
    - 使用prefetch属性可以预先下载资源，以备下一个导航/页面使用，对当前页面没有帮助，所以它的优先级是最低的
    - `<link rel="prefetch"  href="/some_other_resource.jpeg">`
  - 使用dns-prefetch来提前进行DNS解析，以便之后可以快速地访问另一个主机名
    - 浏览器会在加载网页时对网页中的域名进行解析缓存，这样你在之后的访问时无需进行额外的DNS解析，减少了用户等待时间，提高了页面加载速度
    - `<link rel="dns-prefetch" href="other.hostname.com">`
  - prerender
    - 可以预先渲染好页面并隐藏起来，之后打开这个页面会跳过渲染阶段直接呈现在用户面前
    - 推荐对用户接下来必须访问的页面进行预渲染，否则得不偿失
    - `<link rel="prerender"  href="//domain.com/next_page.html">`

#### js

- 虽然浏览器是可以并发请求的，但是脚本（包括外链脚本）在加载时却会阻塞其他资源
- 解决途径：
  - 可以将脚本置底
  - 异步执行：
    - 使用**script**元素的**defer**属性, 用法同disabled（只有IE支持）
      - 规定是否对脚本执行进行延迟，直到页面加载为止
      - 如果脚本不会改变文档的内容，可将 defer 属性加入到 `<script>` 标签中，以便加快处理文档的速度
    - 使用setTimeout、window.onload()或者**JQuery**中的$(function(){})
    - H5 的  **Web Workers** 机制 ？？？？
    - 使用**script**标签中的**async**属性（和disabled用法一样）可以通知浏览器该脚本不需要在引用位置执行
      - 浏览器就可以继续构建DOM，JavaScript脚本会在就绪后开始执行，这样将显著提升页面首次加载的性能
      - async只可以在有src属性的标签中使用也就是外部引用的JavaScript文件
  - 按需加载 （一般用在框架上）
  - 为流量特别大的页面专门定制一个专用的**mini**版框架

#### css

- css在解析过程中是不会阻塞DOM树的处理的
- 浏览器有可能还未下载和解析到**CSS**就已经开始渲染页面, 导致体验很差
- 有些浏览器会在CSS下载完成后才开始渲染页面，如果CSS放在靠下的位置则会导致浏览器将渲染时间推迟
- 解决途径：
  - 将css放在head中
    - 可以保证解析DOM的同时，解析css文件
  - 尽量避免內联样式
    - 可以有效的减少html的体积，一般考虑内联样式的时候，往往是样式本身体积比较小，往往加载网络资源的时间会大于它的时候
  - CSS的媒体查询
    - css文件内部的@media
    - link引入css文件时，media属性可以按条件选择是否参与CSSOM树构建（一定会发送下载请求）

#### 用script标签来异步的请求数据

- 直接在页面上写 `<script>`请求数据会增加页面首次加载的负担，推迟了**DOMLoaded**和**window.onload**事件的触发时机
- 解决途径：
  - 考虑在 DOMLoaded 事件触发的时候加载
  - 使用 setTimeout 方式来灵活的控制加载的时机

#### 减少不必要的HTTP跳转

- 对于以目录形式访问的HTTP链接，如果服务器对链接末尾是否带'/'有区别对待的话,那么其中很可能隐藏了301跳转，增加了多余请求

#### 避免重复的资源请求

- 有可能多个模块中请求了同样的资源，导致资源的重复请求

### 代码级

#### DOM访问与操作

- 对DOM元素以及DOM集合的增删查改最耗性能
- 当有大量DOM操作时，注意：
  - 使用事件代理，避免批量绑定事件
  - 避免在document上直接进行频繁的DOM操作
  - 获取HTML元素集合
    - document.images、document.forms、getElementsByTagName()返回的都是**HTMLCollection**类型的集合，可以作为数组来使用，因为它有length属性，也可以使用索引访问每一个元素，但是性能上则比数组要差很多，原因是这个集合并不是一个静态的结果，它表示的仅仅是一个特定的查询，每次访问该集合时都会重新执行这个查询从而更新查询结果
    - 尽量在要获取元素集合的时候转成数组后在访问，以提高性能
    - 尽可能少访问元素集合，例如访问的时候保存为局部变量

#### 浏览器的 Reflow(回流) & Repaint(重绘)

- 触发回流的操作
  - 调整窗口大小
  - 改变字体font
  - 增加或移除样式表
  - 内容变化，比如用户输入
  - 激活css伪类，比如 **:hover**
  - 操作class属性
  - js操作DOM
  - 计算包括：offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、 clientTop、clientLeft、clientWidth、clientHeight、getBoundingClientRect()、getComputedStyle() (currentStyle in IE8)等属性值
  - 设置style属性的值
- chrome创建单独图层要有以下条件中的一个
  - 一个dom元素拥有3d或透视变换的css属性
  - video 标签
  - 拥有3d上下文或加速2d上下文的canvas节点
  - 混合插件 flash
  - 自己做的opacity动画或使用一个动画webkit变换的元素
  - 拥有 translate3d 或 translateZ 这两会使 GPU 加速的属性
  - 一个包含复合层子节点的元素
  - 元素有一个 **z-index** 比它低的兄弟节点。由于 z-index 控制的是元素上下层的关系，所以当上下层关系变换的时候就需要一个图层去渲染，因此满足这个条件的元素也会被创建一个图层
- 优化方法：
  - 用transform 代替 top，left ，margin-top， margin-left… 这些位移属性
  - 用**opacity** 代替 **visibility**，但是要同时有**translate3d** 或 **translateZ** 这些可以创建图层的属性存在才可以阻止回流
    - 建议：opacity 加上 transform: translateZ/3d 这个属性之后便不会发生回流和重绘了
  - 对于频繁变化的元素应该为其加一个 **transform** 属性，对于视频使用 **video** 标签
  - 使用pointer-events:none
    - 让元素实体 “虚化”，不响应鼠标事件（用 tab 键还是可以选中 a 标签，然后按 enter 打开。这个时候可以去掉 a 标签的 href 属性，就不能让 tab 键选中了）
    - 鼠标操作会直接作用在该元素下一层的元素上（如果能响应）
    - 兼容性：IE10及以下和Firefox3.5及以下不支持
  - 使用will-change属性
    - 告知浏览器该元素会有哪些变化，浏览器可以在元素属性真正发生变化之前提前做好对应的优化准备工作
    - 这种优化可以将一部分复杂的计算工作提前准备好，使页面的反应更为快速灵敏
    - 不要将 will-change 应用到太多元素上
      - 浏览器已经尽力尝试去优化一切可以优化的东西了。有一些更强力的优化，如果与 will-change 结合在一起的话，有可能会消耗很多机器资源，如果过度使用的话，可能导致页面响应缓慢或者消耗非常多的资源
    - 有节制地使用
      - 一般情况下，当元素恢复到初始状态时，浏览器会丢弃掉之前做的优化工作。如果直接添加该属性，会导致浏览器将对应的优化工作一直保存在内存中
      - 最佳实践是当元素变化之前和之后通过脚本来切换 will-change 的值
    - 不要过早应用 will-change 优化
      - will-change 的设计初衷是作为最后的优化手段，用来尝试解决现有的性能问题
      - 过度使用 will-change 会导致大量的内存占用，并会导致更复杂的渲染过程
      - 没有非常必要，则不要滥用
    - 给它足够的工作时间
      - 浏览器可以选择在变化发生前提前去做一些优化工作，优化工作是需要时间的
      - 使用时需要尝试去找到一些方法提前一定时间获知元素可能发生的变化，然后为它加上 will-change 属性
    - 兼容性：只有Chrome3.6以上，Firefox3.6以上，Opera2.4以上才支持
  - 使用 **document.createDocumentFragment()** 插入多个dom
    - DocumentFragments 是DOM节点。它们不是主DOM树的一部分。通常的用例是创建文档片段，将元素附加到文档片段，然后将文档片段附加到DOM树。在DOM树中，文档片段被其所有的子元素所代替
    - 因为文档片段存在于内存中，并不在DOM树中，所以将子元素插入到文档片段时不会引起页面回流

    ```js
    let element  = document.getElementById('ul');
    let fragment = document.createDocumentFragment();
    let li = document.createElement('li');
    fragment.appendChild(li);
    element.appendChild(fragment);
    ```

  - 不要一条一条地修改 DOM 的样式
    - 要预先定义好 css 的 class，然后修改 DOM 的 className，即将多次改变样式属性的操作合并成一次操作
  - 如果一定要对一个元素进行多次操作，先将元素设为**display: none**（需要1次回流和重绘），然后对这个节点进行多次操作，最后再恢复显示（需要1次回流和重绘）
  - 尽可能在DOM树的最末端改变class（即最里面的那个dom节点）
  - 对于复杂的UI元素，设置position为absolute或fixed
    - 脱离文档流的元素重排开销较小(如：position为absolute或fixed，float元素)，因为对文档流中元素无影响
  - 牺牲平滑度换取速度
    - 如果想每次1px移动一个动画，可一旦此动画及随后的回流使用了100%的CPU，动画就会看上去是跳动的，因为浏览器正在与更新回流做斗争。动画元素每次移动3px可能在非常快的机器上看起来平滑度低了，但它不会导致在CPU较慢的机器和移动设备中抖动
  - 避免使用table布局
    - table 的每一个行甚至每一个单元格的样式更新都会导致整个 table 重新布局

#### 移动端优化

- 长列表滚动优化
  - IOS尽量使用局部滚动，android尽量使用全局滚动
  - 同时需要给body添加上-webkit-overflow-scrolling: touch来优化移动端的滚动
- 函数防抖和函数节流 （涉及到滚动等会被频繁触发的DOM事件，需要做好防抖和节流的工作）
  - 函数防抖：当调用动作过n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间
  - 函数节流：预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执行该动作，然后进入下一个新周期
  - 两者都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟，假死或卡顿的现象
- touchstart、touchend代替click（或者使用fastclick或者zepto的tap事件代替click事件）
  - click在移动端会有300ms延时，点击事件的执行顺序是touchstart->touchmove->touchend->click
- HTML的viewport设置
  - 可以防止页面的缩放，来优化性能
- 开启GPU渲染加速
  - GPU的全名是Graphics Processing Unit，是一种硬件加速方式，一般的css渲染，浏览器的渲染引擎都不会使用到它
  - 在3D渲染时，计算量较大，繁重，浏览器会开启显卡的硬件加速来帮助完成这些操作
  - 可以使用css中的translateZ设定，来欺骗浏览器，让其帮忙开启GPU加速，加快渲染进程
  
#### 慎用with()

- 使用**with()**相当于增加了作用域链长度
- 查找作用域链是要消耗时间的，过长的作用域链会导致查找性能下降

#### 慎用 **eval()** 和 **Function**

- 每次 eval 或 Function 构造函数作用于字符串表示的源代码时，脚本引擎都需要将源代码转换成可执行代码。这是很消耗资源的操作, 通常比简单的函数调用慢100倍以上
  - eval 函数效率特别低，由于事先无法知晓传给 eval 的字符串中的内容，eval在其上下文中解释要处理的代码，也就是说编译器无法优化上下文，因此只能有浏览器在运行时解释代码
  - Function 构造函数比 eval 略好，因为使用此代码不会影响周围代码, 但其速度仍很慢
  - 使用 eval 和 Function 也不利于 Javascript 压缩工具执行压缩

#### 减少作用域链查找(尤其在循环中)

- 应该减少闭包的使用
- 如果在循环中需要访问非本作用域下的变量时请在遍历之前用局部变量缓存该变量，并在遍历结束后再重写那个变量
  - 这一点对全局变量尤其重要，因为全局变量处于作用域链的最顶端，访问时的查找次数是最多的

```js
var globalVar = 1;
function myCallback(info){
  for( var i = 100000; i--;){
    //每次访问 globalVar 都需要查找到作用域链最顶端，本例中需要访问 100000 次
    globalVar += i;
  }
}

// 改成

var globalVar = 1;
function myCallback(info){
  //局部变量缓存全局变量
  var localVar = globalVar;
  for( var i = 100000; i--;){
    //访问局部变量是最快的
    localVar += i;
  }
  // 本例中只需要访问 2次全局变量
  // 在函数中只需要将 globalVar中内容的值赋给localVar 中区
  globalVar = localVar;
}
```

#### 数据访问

- Javascript中的数据访问包括直接量(字符串、正则表达式)、变量、对象属性以及数组
  - 对直接量和局部变量的访问是最快的
  - 对对象属性以及数组的访问需要更大的开销
- 以下情况建议将数据放入局部变量
  - 对任何对象属性的访问超过1次
  - 对任何数组成员的访问次数超过1次
- 应当尽可能的减少对对象以及数组深度查找

#### 字符串拼接

- 在Javascript中使用 "+" 号来拼接字符串效率是比较低的，因为每次运行都会开辟新的内存并生成新的字符串变量，然后将拼接结果赋值给新变量
- 更为高效的做法是使用数组的 join 方法，即将需要拼接的字符串放在数组中，最后调用其 join 方法得到结果
  - 由于使用数组也有一定的开销，因此当需要拼接的字符串较多的时候可以考虑用此方法

#### css样式

- 浏览器对选择符的解析是从右往左进行的

```css
/* 浏览器必须遍历查找每一个a标签的祖先节点, 此时效率低 */
#toc a { color: #444; }
```

- 如果规则拥有 ID 选择器作为其关键选择器，则不要为规则增加标签。过滤掉无关的规则（这样样式系统就不会浪费时间去匹配它们了）
  - 选择器的最右边的部分为关键选择器（即用来匹配目标元素的部分）
- 不要用CSS通配符 *
- 提取项目的通用公有样式，增强可复用性，按模块编写组件
  - 同时可以增强项目的协同开发性、可维护性和可扩展性
- 使用预处理工具
- 尽量使用css动画
  - 相对于js动画，浏览器本身对css动画进行了优化，使用上面不会出现卡顿等问题
- 使用requestAnimationFrame代替setInterval和settimeout操作
  - 详[canvas动画](/H5/H5-canvas.md)
- 适当使用canvas
  - canvas使用频繁的话，会加重浏览器渲染的压力，同时导致性能的下降
- 尽量减少css表达式的使用
  - 在使用过程中，由于其频繁触发的特性，会拖累网页的性能，出现卡顿。因此在使用过程中尽量减少css表达式的使用，可以改换成js进行操作
- 操作细节注意（影响较小）
  - 避免图片或者frame使用空src
  - 在css属性为0时，去掉单位
  - 禁止图像缩放
  - 正确的css前缀的使用
  - 移除空的css规则
  - 对于css中可继承的属性，如font-size，尽量使用继承，少一点设置
  - 缩短css选择器（尽量不要超过三层），多使用伪元素等帮助定位

#### HTML优化 ？？？？？

- gzip压缩可以将html中重复的部分进行一个打包，多次复用
- 首屏只加载部分HTML
  - 服务端在接收到请求时先只响应回HTML的初始部分，后续的HTML内容在需要时再通过AJAX获得
  - 这个方法不能用在CSS上，浏览器不允许CSSOM只构建初始部分，否则会无法确定具体的样式

#### CDN加速 ？？？？

- CDN（content distribute network，内容分发网络）的本质仍然是一个缓存，而且将数据缓存在离用户最近的地方，使用户以最快速度获取数据

#### 反向代理

- 详[反向代理服务器](/base/http.md)