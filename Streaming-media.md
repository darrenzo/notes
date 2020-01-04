# Streaming media

## 顺序流式传输（http流式传输/点播技术）

- 在观看前，必须经历延迟，但能保证图像质量
- 比较适合高质量的短片段，不适合长片段和有随机访问要求的视频，也不支持现场广播
- 顺序流式文件是放在标准HTTP或FTP服务器上，易于管理，基本上与防火墙无关

## 实时流式传输

- 保证媒体信号带宽与网络连接配匹，使媒体可被实时观看到
- 理论上，实时流一经播放就可不停止，但实际上，可能发生周期暂停
- 不能保证图像质量
- 总是实时传送，特别适合现场事件，也支持随机访问
- 需要专用的流媒体服务器(QuickTime Streaming Server、RealServer与Windows Media Server)，这些服务器允许你对媒体发送进行更多级别的控制，因而系统设置、管理比标准HTTP服务器更复杂
- 需要专用的传输协议(RTSP、MMS)，有可能受到防火墙的影响，导致用户不能看到一些地点的实时内容
- 实现流式传输一般都需要专用服务器和播放器

## 产品组成

- 编码器： （Microvision流媒体采集卡）  将音视频信息源输入 （普通计算机），计算机中的 （流媒体编码软件） 将数字音视频信号压缩成流媒体格式（如果做直播，它还负责实时地将压缩好的流媒体信号上传给流媒体服务器）

- 服务器： （流媒体软件系统的服务器部分） + （硬件服务器） 。 负责管理、存储、分发编码器传上来的流媒体节目。

- 终端播放器（解码器）： （流媒体系统的播放软件） + （普通计算机） 。 播放用户想要收看的流媒体服务器上的视频节目。

## 原理

- Internet以包传输为基础进行断续的异步传输，所以流式传输的实现需要缓存
- 通常高速缓存所需容量并不大，因为高速缓存使用环形链表结构来存储数据：通过丢弃已经播放的内容，流可以重新利用空出的高速缓存空间来缓存后续尚未播放的内容

## 流媒体格式（声音流、视频流、文本流、图像流、动画流）

- RA:实时声音

  RM:实时视频或音频的实时媒体

  RT:实时文本 RP:实时图像

  SMIL:同步的多重数据类型综合设计文件

  SWF：macromedia 的 real flash 和 shockwave flash动画文件

  RPM：HTML文件的插件

  RAM：流媒体的元文件，是包含RA、RM、SMIL文件地址（URL地址）的文本文件

  CSF：一种类似媒体容器的文件格式，可以将非常多的媒体格式包含在其中，而不仅仅限于音、视频。

## 传输协议

- RSVP：资源预留协议

  RTP：实时传输协议
  
  RTCP：实时传输控制协议
  
  MMS：微软流媒体服务协议
  
  RTSP：实时流传输协议
  
  MIME：多目因特网电子邮件扩展协议
  
  RTMP(RTMPE/RTMPS/RTMPT)：Adobe实时消息协议簇
  
  RTMFP：Adobe实施消息流协议（P2P协议）

## 播放方式

- 单播

  组播

  点播

  广播

## FLV流媒体格式

- FLV 是`FLASH VIDEO`的简称
- 它形成的文件极小、加载速度极快，使得网络观看视频文件成为可能
- 有效地解决了视频文件导入Flash后，使导出的**SWF**文件体积庞大，不能在网络上很好的使用等问题
- 能起到保护版权的作用，并且可以不通过本地的微软或者REAL播放器播放视频
- 网站的访问者只要能看Flash动画，自然也能看FLV格式视频，而无需再额外安装其它视频插件
  
### 文件结构

- FLV是一个二进制文件
- FLV文件=FLV头文件+ tag1+tag内容1 + tag2+tag内容2 + ...+... + tagN+tag内容N
  - tag又可以分成三类：audio,video,script，分别代表音频流，视频流，脚本流（关键字或者文件信息之类）

#### 文件头（FLV header）

- 一般比较简单，包括文件类型之类的全局信息, 大小一般为9字节
- 包括以下
  - 1-3： 前3个字节是文件格式标识(FLV 0x46 0x4C 0x56)
  - 4-4： 第4个字节是版本（0x01），表示FLV version 1
  - 5-5： 第5个字节的前5个bit是保留的必须是0
    - 流信息1byte 倒数第一bit是1表示有视频，倒数第三bit是1表示有音频，其他都应该是0（有些软件如flvtool2可能造成倒数第四bit是1，不过也没发现有什么不对）
  - 6-9: 第6-9的四个字节还是保留的. 其数据为 00000009
    - header长度4bytes 整个文件头的长度，一般是9（3+1+1+4），有时候后面还有些别的信息，就不是9了

#### 文件元信息（metadata）

- ScriptTagData结构（控制帧）
- 会放一些关于FLV视频和音频的参数信息如：duration、width、height等
- 通常该类型Tag会跟在File Header后面作为第一个Tag出现，而且只有一个
- 前11个字节为Tag Header结构
- 第一个AMF包（13字节）：
  - 第1个字节表示AMF包类型，一般总是0x02，表示字符串
  - 第2-3个字节为UI16类型值，一般总是0x000A（“onMetaData”长度）
  - 后面字节一般总为“onMetaData”。（6F,6E,4D,65,74,61,44,61,74,61）
- 第二个AMF包：
  - 第1个字节表示AMF包类型，一般总是0x08，表示数组
  - 第2-5个字节为UI32类型值，表示数组元素的个数
  - 后面即为各数组元素的封装，数组元素为元素名称和值组成的对
    - 表示方法如下： 第1-2个字节表示元素名称的长度，假设为L， 后面跟着长度为L的字符串， 第L+3个字节表示元素值的类型，后面跟着为对应值，占用字节数取决于值的类型

#### FLV Body

- FLV body就是由很多tag组成的
- tag类型信息,固定长度为15字节
  - 1-4: 前一个tag长度(4字节),第一个tag就是0
  - 5-5: tag类型(1字节);0x8音频；0x9视频；0x12脚本数据
  - 6-8: tag内容大小(3字节)
  - 9-11: 时间戳(3字节)(毫秒)(第1个tag的时候总是为0,如果是脚本tag就是0)
  - 12-12: 时间戳扩展(1字节)让时间戳变成4字节(以存储更长时间的flv时间信息),本字节作为时间戳的最高位
    - 在flv回放过程中,播放顺序是按照tag的时间戳顺序播放.任何加入到文件中时间设置数据格式都将被忽略
  - 13-15: streamID(3字节) 总是0
- tag数据区
  - 根据不同的tag类型就有不同的数据区
  - Audio tag 数据区（1byte）
    - 前四位bits表示音频格式:
      - 0 -- 未压缩
      - 1 -- ADPCM
      - 2 -- MP3
      - 5 -- Nellymoser 8kHz momo
      - 6 -- Nellymoser
    - 下面两位bits表示取样频率:
      - 0 -- 5.5kHz
      - 1 -- 11kHz
      - 2 -- 22kHz
      - 3 -- 44kHz
    - 下面一位bit表示每个采样的长度:
      - 0 -- snd8Bit
      - 1 -- snd16Bit
    - 下面一位bit表示类型:
      - 0 -- sndMomo
      - 1 -- sndStereo
  - Audio 数据区  不确定
  - video tag 数据区（1byte）
    - 前四位bits表示类型：
      - 1 -- keyframe
      - 2 -- inner frame
      - 3 -- disposable inner frame (H.263 only)
    - 后四位bits表示编码器id：
      - 2 -- Seronson H.263
      - 3 -- Screen video
      - 4 -- On2 VP6
      - 5 -- On2 VP6 without channel
      - 6 -- Screen video version 2
  - video 数据区 不确定

### [flv 格式图解](http://www.luyixian.cn/news_show_21004.aspx)

## flv.js

- [github地址](https://github.com/bilibili/flv.js)
  - 在执行 **gulp-release** 时会出现 `const { Math, Object } = primordials;` 报错，因为需要 node 版本是 v10.X.X 版本
  - 也可以直接 npm 安装，此时不需要重新构建，已经有dist文件
- 参考[gwuhaolin](https://github.com/gwuhaolin/blog/issues/3)
- 各大浏览器厂商禁用 flash，需要使用 html5 原生 video 播放器
- 常见直播协议
  - RTMP: 底层基于TCP，在浏览器端依赖Flash
    - 播放端安装率高。只要浏览器支持FlashPlayer就能非常简易的播放
  - HTTP-FLV: 基于HTTP流式IO传输FLV，依赖浏览器，支持播放FLV
    - 播放端安装率高。只要浏览器支持FlashPlayer就能非常简易的播放
  - WebSocket-FLV: 基于WebSocket传输FLV，依赖浏览器，支持播放FLV
    - WebSocket建立在HTTP之上，建立WebSocket连接前还要先建立HTTP连接
  - HLS: Http Live Streaming，苹果提出基于HTTP的流媒体传输协议(PC端只有 Safari 支持 HLS)
    - HTML5可以直接打开播放；这个意味着可以把一个直播链接通过微信等转发分享，不需要安装任何独立的APP，有浏览器即可
  - RTP: 基于UDP，延迟1秒，浏览器不支持
- rtmp 和 http-flv 比较
  - 穿墙：很多防火墙会墙掉RTMP，但是不会墙HTTP，因此 HTTP FLV 出现奇怪问题的概率很小
  - 调度：RTMP 也有个302，可惜是 播放器as 中支持的，HTTP FLV流 就支持302，方便 CDN 纠正 DNS 的错误
  - 容错：SRS 的 HTTP FLV 回源时可以回多个，和 RTMP 一样，可以支持多级热备
  - 简单：FLV 是最简单的流媒体封装，HTTP 是最广泛的协议，这两个组合在一起维护性更高，比 RTMP 简单多了
- 协议延迟与性能数据对比参考

```shell
  | 传输协议  | 播放器 | 延迟 | 内存   | CPU  |
  |:--------:|:------:|:---:|:----- :|:----:|
  | RTMP     | Flash  | 1s  |  430M  | 11%  |
  | HTTP-FLV | Video  | 1s  |  310M  | 4.4% |
  | HLS      | Video  | 20s |  205M  | 3%   |
```

- flv.js只做了一件事，在获取到FLV格式的音视频数据后通过原生的JS去解码FLV数据，再通过 Media Source Extensions API 喂给原生HTML5 Video标签
  - HTML5 原生仅支持播放 mp4/webm 格式，不支持 FLV
- 默认使用了 HTTP FLV 协议，Httpflv是一种将 rtmp 等负载信息携带在 http 协议之上的码流传递协议，特性是可以更好的穿透防火墙。取流的时候需要向服务端发送一个http请求
  - 类似于 websocket，httpflv 也是利用http协议做了连接，然后丢弃掉http协议直接利用该tcp通道进行码流传输
    - httpflv 传输码流的部分中间会有 \r\n**\r\n 来分割一个 tagsize 和它的 tagheader
  - 大部分的视频网站都使用了 HTTP FLV 协议
- http-flv 技术实现
  - HTTP协议中有个约定：
    - 服务器回复 http 请求的时候如果有content-length 字段(http 的 body 部分的长度)，客户端就接收这个长度的数据然后就认为数据传输完成了
    - 如果服务器回复 http 请求中没有这个字段，客户端就一直接收数据，直到服务器跟客户端的 socket 连接断开
  - http-flv 直播就是服务器回复客户端请求的时候不加 content-length 字段，在回复了 http 内容之后，紧接着发送 flv 数据，客户端就一直接收数据了
- 使用和测试 http-flv
  - 使用开源的 SRS 服务器(SRS2.0 版本是开源，后续的版本已经闭源)
  - 推流端以 rtmp 的形式推流到 srs 服务器，srs 再把 rtmp 流转换成 http-flv 流
  
  ```shell
  ## 请求SRS返回的是：
  HTTP/1.1 200 OK
  Connection: Keep-Alive
  Content-Type: video/x-flv
  Server: SRS/2.0.205
  Transfer-Encoding: chunked

  ## 请求某个电视台 http://fms.cntv.lxdns.com/live/flv/channel2.flv ，返回的是：
  HTTP/1.1 200 OK
  Cache-Control: no-cache
  Content-Type: video/x-flv
  Expires: -1
  ```

- flv.js 解决的问题
  - 兼容目前的直播方案：目前大多数直播方案的音视频服务都是采用FLV容器格式传输音视频数据
  - FLV容器格式相比于MP4格式更加简单，解析起来更快更方便
- flv.js 的优势
  - 由于浏览器对原生Video标签采用了硬件加速，性能很好，支持高清
  - 同时支持录播和直播
  - 去掉对Flash的依赖
- flv.js 的限制
  - FLV里所包含的视频编码必须是H.264，音频编码必须是AAC或MP3，IE11和Edge浏览器不支持MP3音频编码，所以FLV里采用的编码最好是H.264+AAC
  - 对于录播，依赖原生 HTML5 Video 标签 和 Media Source Extensions API
  - 对于直播，依赖录播所需要的播放技术，同时依赖 HTTP FLV 或者 WebSocket 中的一种协议来传输FLV
    - 其中 HTTP FLV 需通过 流式IO 去拉取数据，支持 流式IO 的有 fetch 或者 stream
  - flv.min.js 文件大小 169Kb，gzip后 35.5Kb，flash播放器gzip后差不多也是这么大
- 由于依赖 Media Source Extensions，部分 iOS 版本 和 Android4.4.4 以下里的浏览器不支持
  - 依赖项：HTML5 Video、Media Source Extensions、WebSocket、HTTP FLV: fetch 或 stream
- 兼容方案
  - 优先使用 HTTP-FLV，因为它延迟小，性能也不差1080P都很流畅
  - 不支持 flv.js 就使用 Flash播放器播 RTMP 流（移动端不支持flash）
    - Flash兼容性很好，但是性能差默认被很多浏览器禁用
  - 不想用 Flash 兼容也可以用 HLS ，但是PC端只有 Safari 支持 HLS
- 搭建音视频服务的思路： 服务端启动RTMP(1935端口)服务用于主播推流，以及HTTP-FLV(7001端口)服务用于播放
  - 直播运行流程：
    - 主播端在采集到一段时间的音视频原数据后，因为音视频原数据庞大需要先压缩数据
      - 通过 H264 视频编码 压缩数据
      - 通过 PCM 音频编码 压缩音频 AAC 数据
    - 压缩完后再通过 FLV 容器格式 封装压缩后的数据，封装成一个FLV TAG
    - 再把 FLV TAG 通过 RTMP 协议推流到音视频服务器，音视频服务器再从 RTMP 协议里解析出 FLV TAG
    - 音视频服务器再通过 HTTP 协议通过和浏览器建立的长链接流式把 FLV TAG 传给浏览器
    - flv.js 获取 FLV TAG 后解析出压缩后的音视频数据喂给Video播放
  - 延迟优化
    - 主播端采集时收集了一段时间的音视频原数据，它专业的叫法是GOP。缩短这个收集时间(也就是减少GOP长度)可以优化延迟，但这样做的坏处是导致视频压缩率不高，传输效率低
    - 关闭音视频服务器的I帧缓存可以优化延迟，坏处是用户看到直播首屏的时间变大
    - 减少音视频服务器的 buffer 可以优化延迟，坏处是音视频服务器处理效率降低
    - 减少浏览器端 flv.js 的 buffer 可以优化延迟，坏处是浏览器端处理效率降低
    - 浏览器端开启 flv.js 的 Worker，多线程运行 flv.js 提升解析速度可以优化延迟

    ```js
    {
      enableWorker: true, // 开启webworker
      enableStashBuffer: false, // 不开启 IO 储存缓冲区
      stashInitialSize: 128 // 减少首帧显示等待时长，默认是384
    }
    ```
