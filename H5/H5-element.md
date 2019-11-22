# html5 Element

## 全局属性

- class：规定元素的一个或多个类名（引用样式表中的类）
- id: 规定元素的唯一 id
- style: 规定元素的行内 CSS 样式
- title：规定有关元素的额外信息
- contenteditable：规定元素内容是否可编辑，
  - 值是 true|false
  - 如果元素未设置 contenteditable 属性，那么元素会从其父元素继承该属性
- data-*：用于存储页面或应用程序的私有定制数据
  - 属性名不应该包含任何大写字母，并且在前缀 "data-" 之后必须有至少一个字符
  - 属性值可以是任意字符串
  - 用户代理会完全忽略前缀为 "data-" 的自定义属性
- dir：规定元素内容的文本方向
  - 值是 ltr|rtl
  - dir 属性在以下标签中无效：`<base>`, `<br />`, `<frame>`, `<frameset>`, `<hr />`, `<iframe>`, `<param>` 以及 `<script>`
- lang: 规定元素内容的语言
  - lang 属性在以下标签中无效：`<base>`, `<br />`, `<frame>`, `<frameset>`, `<hr />`, `<iframe>`, `<param>` 以及 `<script>`
- draggable: 规定元素是否可拖动
  - 值是 true|false|auto
  - 链接和图像默认是可拖动的
- spellcheck: 规定是否对元素进行拼写和语法检查
  - 值是 true|false
  - 检查范围
    - input 元素中的文本值（非密码）
    - textarea 元素中的文本
    - 可编辑元素中的文本
- accesskey：规定激活（使元素获得焦点）元素的快捷键
  - Opera 不支持
  - 使用方法：Alt + accessKey (或者 Shift + Alt + accessKey) 访问
- hidden：规定元素仍未或不再相关，元素不可见
  - hidden
  - IE 不支持
- tabindex: 规定元素的 tab 键控制次序（当 tab 键用于导航时）
  - 值是 number ( 1 是第一个)
  - Safari 不支持
  - 以下元素支持 tabindex 属性：`<a>`, `<area>`, `<button>`, `<input>`, `<object>`, `<select>` 以及 `<textarea>`

## window 事件属性

- 针对 window 对象触发的事件（应用到 `<body>` 标签）
  - afterprint: 文档打印之后
  - beforeprint: 文档打印之前
  - beforeunload: 文档卸载之前
  - error: 在错误发生时
  - haschange: 当文档已改变时
  - load: 页面结束加载之后
  - unload：一旦页面已下载时触发（或者浏览器窗口已被关闭）
  - message：在消息被触发时
  - offline：当文档离线时
  - online：当文档上线时
  - pagehide：当窗口隐藏时
  - pageshow：当窗口成为可见时
  - popstate：当窗口历史记录改变时
  - redo：当文档执行撤销（redo）时
  - undo：在文档执行 undo 时
  - resize：当浏览器窗口被调整大小时触发
  - storage：在 Web Storage 区域更新后

## `<abbr>`

- 表示一个缩写形式

```html
<abbr title="People's Republic of China">PRC</abbr>
```

## `<address>`

- 定义文档作者或拥有者的联系信息
- 通常是将 address 元素添加到网页的头部或底部

```html
<address>
  Written by zww<br />
  <a href="mailto:zww@example.org">Email me</a><br />
  Address: zg<br />
  Phone: +12 34 56 78
</address>
```

## `<area>`

- 定义图像映射内部的区域（图像映射指的是带有可点击区域的图像）
- area 元素始终嵌套在 `<map>` 标签内部
- 属性
  - alt：规定区域的替代文本。如果使用 href 属性，则该属性是必需的
  - coords：规定区域的坐标
  - href：规定区域的目标 URL
  - hreflang：规定目标 URL 的语言
  - media：规定目标 URL 是为何种媒介/设备优化的。默认：all
  - rel：规定当前文档与目标 URL 之间的关系
    - alternate：文档的替代版本（比如打印页、翻译或镜像）
    - author：链接到文档的作者
    - bookmark：用于书签的永久网址
    - external：告诉搜索引擎，这个链接不是本站链接，作用相当于target='_blank'，某些场景下可作为替代，常和nofollow一起使用
    - help：链接到帮助文档
    - license：链接到文档的版权信息
    - prev：选项中的前一个文档
    - next：选项中的下一个文档
    - nofollow：告诉搜索引擎"不要追踪此网页上的链接"或"不要追踪此特定链接"
    - noreferrer：如果用户点击链接指定浏览不要发送 HTTP referer 头部信息
    - prefetch：指定的目标文件应该被缓存
    - search：文档链接到搜索工具
    - sidebar：链接到应该在浏览器边栏中显示的文档
    - tag：当前文档的标签(关键词)
  - shape：规定区域的形状
    - 如果未使用shape属性，那么会假设使用值default，意味着该区域覆盖整个图像
    - 实际中，浏览器默认使用矩形区域，并期望能找到 4 个 coords 值。如果没有指定形状，而且在标签中也没有包括 4 个坐标，那么浏览器会忽略整个区域
    - 由于设备识别的因素，shape值推荐使用简写的字段
    - 规定全部区域（default）
    - 圆形（circ 或 circle）
    - 多边形（poly 或 polygon）
    - 矩形（rect 或 rectangle）
  - target：规定在何处打开目标 URL
    - _blank
    - _self
    - _parent：在父框架集中打开被链接文档
    - _top：在整个窗口中打开被链接文档
    - framename：在指定的框架中打开被链接文档
  - type：规定目标 URL 的 MIME 类型

## `<map>`

- 定义客户端图像映射。图像映射指的是带有可点击区域的图像
- name 属性在 map 元素中是必需的, 该属性与 `<img>` 标签的 usemap 属性相关联，以创建图像与映射之间的关系
- map 元素包含若干 area 元素，定义图像映射中的可点击区域

```html
<img src="planets.gif" alt="Planets" usemap ="#planetmap" />

<map name="planetmap">
   <area shape ="rect" coords ="0,0,110,260" href ="sun.htm" alt="Sun" />
   <area shape ="circle" coords ="129,161,10" href ="mercur.htm" alt="Mercury" />
   <area shape ="circle" coords ="180,139,14" href ="venus.htm" alt="Venus" />
</map>
```

## `<article>`

- 定义独立的内容
- 可能的 article 实例
  - 论坛帖子
  - 报纸文章
  - 博客条目
  - 用户评论

```html
<article>
  <a href="http://www.apple.com">Safari 5 released</a><br />
  7 Jun 2010. Just after the announcement of the new iPhone 4 at WWDC,
  Apple announced the release of Safari 5 for Windows and Mac......
</article>
```

## `<aside>`

- 定义其所处内容之外的内容
- aside 的内容应该与附近的内容相关
- `<aside>` 的内容可用作文章的侧栏

```html
<p>Me and my family visited The Epcot center this summer.</p>
<aside>
  <h4>Epcot Center</h4>
  The Epcot Center is a theme park in Disney World, Florida.
</aside>
```

## `<base>`

- 为页面上的所有链接规定默认地址或默认目标
- 通常情况下，浏览器会从当前文档的 URL 中提取相应的元素来填写相对 URL 中的空白, 使用 `<base>` 标签后，浏览器随后将不再使用当前文档的 URL，而使用 base 指定的基本 URL 来解析所有的相对 URL
  - 包括 `<a>`、`<img>`、`<link>`、`<form>` 标签中的 URL
- `<base>` 标签必须位于 head 元素内部, 推荐放在 head 元素中第一个元素的位置
- 在一个文档中，最多能使用一个 `<base>` 元素
- 属性
  - href：规定作为基准 URL 在页面中使用的 URL
  - target：在何处打开页面上的链接。该属性会被每个链接中的 target 属性所覆盖

```html
<head>
    <base href="http://www.w3school.com.cn/i/" />
</head>

<!-- 最终查找的是 http://www.w3school.com.cn/i/pic.gif -->
<img src="pic.gif" />
```

## `<bdo>`

- 覆盖默认的文本方向

```html
<bdo dir="rtl">Here is some text</bdo>
<!-- 输出 Here is some text -->
<!-- 复制此处文本，得到的则会是正序的 -->
```

## `<blockquote>`

- 定义摘自另一个源的块引用
- `<blockquote>` 与 `</blockquote>` 之间的所有文本都会从常规文本中分离出来，经常会在左、右两边进行缩进，而且有时会使用斜体。也就是说，块引用拥有它们自己的空间
- blockquote 只应该应用于来自另一个源的引用
- 如果标记是不需要段落分隔的短引用，请使用 `<q>` 元素
- 属性
  - cite：引用的 URL，如果引用来自网络的话

```html
<p>
    Here is a quote from WWF's website:
    <blockquote cite="http://www.wwf.org">
        WWF's ultimate goal is to build a future where people live in harmony with nature.
    </blockquote>
    We hope that they succeed.
</p>
```

## `<q>`

- 定义一个短的引用
- 浏览器经常会在这种引用的周围插入引号
- 与 `<blockquote>` 一样有个 cite 属性

```html
<p>
    WWF's goal is to:
    <q cite="http://www.wwf.org">
        build a future where people live in harmony with nature
    </q>
    we hope they succeed.
</p>

<!-- 输出 WWF's goal is to: " build a future where people live in harmony with nature " we hope they succeed. -->
<!-- 复制此处文本，得到的文本中引号位置会变成空格 -->
```

## `<col />`

- 为表格中的一个或多个列定义属性值
- 通过使用 col 标签，可以向整个列应用样式，而不需要重复为每个单元格或每一行设置样式
- 只能在 table 或 colgroup 元素内使用 col 标签
- col 元素是空元素
- 可以给该元素加个class，通过css编写样式

## `colgroup`

- 用于对表格中的列进行组合，以便对其进行格式化
- 只能在 table 元素内使用 colgroup 标签
- 如果希望为一组表格列规定相同的属性值，请使用 colgroup 元素
- 如果希望为多个表格列规定不同的属性值，请使用 col 元素

## `<col />` 与 `<colgroup>` 的区别

- 两者均可使用 span 属性来设置应用的列数
- 后面的标签所应用的列范围都是在前面的标签所应用的范围往后算
  - 如果第一个 col 的 span=2，则第二个 col 的 span 是从第三个开始数
- 如果 colgroup 内部设置了 col 则 colgroup 标签的span属性不生效，而是根据 col 标签的 span 属性应用，col 标签上设置的样式会覆盖 colgroup 的样式

```html
<table border="1">
  <colgroup class="colgroup1" span="3">
      <col class="col1" span="1">
      <col class="col2" span="1">
  </colgroup>

  <colgroup class="colgroup2" span="2">
      <col class="col3" span="1">
  </colgroup>
  <tr>
    <th>Price</th>
    <th>Price</th>
    <th>Price</th>
    <th>Price</th>
    <th>Price</th>
  </tr>
  <tr>
    <td>Price</td>
    <td>Price</td>
    <td>Price</td>
    <th>Price</th>
    <td>Price</td>
  </tr>
</table>
```

## `<datalist>`

- 定义选项列表
- 与 input 元素配合使用该元素，来定义 input 可能的值
- 使用 input 元素的 list 属性来绑定 datalist
- datalist 及其选项不会被显示出来，它仅仅是合法的输入值列表
- 输入时会显示提示，类似于搜索提示框
- 输入时，输入框内部靠右会有向下箭头
- IE和Safari不支持

```html
<input list="cars" />

<datalist id="cars">
    <!-- 此处option没有结束标签 -->
    <option value="BMW">
    <option value="Ford">
    <option value="Volvo">
</datalist>
```

## `<fieldset>`

- 用于从逻辑上将表单中的元素组合起来
- 标签会在相关表单元素周围绘制边框
- `<fieldset>` 标签将表单内容的一部分打包，生成一组相关表单的字段
- 当一组表单元素放到 `<fieldset>` 标签内时，浏览器会以特殊方式来显示它们，它们可能有特殊的边界、3D 效果，或者甚至可创建一个子表单来处理这些元素
- `<legend>` 标签为 fieldset 元素定义标题
- 属性
  - disabled：规定应该禁用 fieldset，即表单元素无法输入
  - form：规定 fieldset 所属的一个或多个表单, 值是form表单的id
  - name：规定 fieldset 的名称

```html
<form>
  <fieldset>
    <legend>健康信息</legend>
    身高：<input type="text" />
    体重：<input type="text" />
  </fieldset>
</form>
```

## `<figure>`

- 规定独立的流内容（图像、图表、照片、代码等等）
- figure 元素的内容应该与主内容相关，但如果被删除，则不应对文档流产生影响

```html
<p>The Pulpit Rock is a massive cliff 604 metres (1982 feet) above Lysefjorden, opposite the Kjerag plateau, in Forsand, Ryfylke, Norway. The top of the cliff is approximately 25 by 25 metres (82 by 82 feet) square and almost flat, and is a famous tourist attraction in Norway.</p>

<figure>
  <img src="img_pulpit.jpg" alt="The Pulpit Rock" width="304" height="228">
  <figcaption>Fig.1 - The Pulpit Rock, Norway.</figcaption>
</figure>
```

## `<figcaption>`

- 定义 figure 元素的标题
- figcaption 元素应该被置于 figure 元素的第一个或最后一个子元素的位置

## `<header>` 和 `<footer>`

- 分别用来定义 section 或 page 的页眉和页脚
- 都放在 body 里

## `<hgroup>`

- 用于对网页或区段（section）的标题进行组合

```html
<hgroup>
  <h1>Welcome to my WWF</h1>
  <h2>For a living planet</h2>
</hgroup>

<p>The rest of the content...</p>
```

## `<mark>`

- 定义带有记号的文本
- 把内容所在的矩形区域背景变成黄色

```html
<p>Do not forget to buy <mark>milk</mark> today.</p>
```

## `<meta />`

- 提供有关页面的元信息, 比如针对搜索引擎和更新频度的描述和关键词
- 位于文档的头部，不包含任何内容。标签的属性定义了与文档相关联的名称/值对
- 标签永远位于 head 元素内部
- 元数据总是以名称/值的形式被成对传递的
- 属性
  - charset：定义文档的字符编码
  - content：定义与 http-equiv 或 name 属性相关的元信息
  - http-equiv：把 content 属性关联到 HTTP 头部
    - content-Type: 设定页面使用的字符集
      - `<meta http-equiv="content-Type" content="text/html;charset=gb2312" />`
    - charset: 指定使用的字符编码
      - `<meta http-equiv="charset" content="iso-8859-1">`
    - expires：设定网页的到期时间，一旦网页过期，必须到服务器上重新传输
      - `<meta http-equiv="expires" content="Fri,12Jan200118:18:18GMT" />` 必须使用GMT格式
    - Refresh：自动刷新并指向新页面
      - `<meta http-equiv="Refresh" content="2;URL=http://www.jb51.net" />`, 网页在2秒后跳转到对应的URL，如果不设置URL，则跳转到当前页，即刷新
    - Set-Cookie：cookie设定
      - 如果网页过期，那么存盘的cookie将被删除
      - `<meta http-equiv="Set-Cookie" content="cookievalue=xxx;expires=Friday,12-Jan-200118:18:18GMT；path=/" />`, 必须使用GMT格式
    - Pragma：禁止浏览器从本地计算机的缓存中访问页面内容
      - `<meta http-equiv="Pragma" content="no-cache" />` 禁止缓存，则访问者无法脱机浏览
    - Window-target：显示窗口的设定
      - 强制页面在当前窗口以独立页面显示，用来防止别人在框架里调用自己的页面
      - `<meta http-equiv="Window-target" content="_top" />`
    - Content-Language: 显示语言的设定
      - `<meta http-equiv="Content-Language" content="zh-cn" />`
    - Cache-Control: 指定请求和响应遵循的缓存机制
      - 在请求消息或响应消息中设置Cache-Control并不会修改另一个消息处理过程中的缓存处理过程
      - Public: 指示响应可被任何缓存区缓存
      - Private: 指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当前用户的部分响应消息，此响应消息对于其他用户的请求无效
      - no-cache：指示请求或响应消息不能缓存
      - no-store：用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存
      - max-age：指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应
      - min-fresh：指示客户机可以接收响应时间小于当前时间加上指定时间的响应
      - max-stale：指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息
  - name：把 content 属性关联到一个名称
    - author: 标注网页的作者
    - description
    - keywords: 关键字
    - generator
    - revised
    - robots：用来告诉搜索机器人当前页面是否需要索引
      - all：文件将被检索，且页面上的链接可以被查询，默认是all
      - none：文件将不被检索，且页面上的链接不可以被查询
      - index：文件将被检索
      - noindex：文件将不被检索，但页面上的链接可以被查询
      - follow：页面上的链接可以被查询
      - nofollow：文件将被检索，但页面上的链接不可以被查询

## `<nav>`

- 定义导航链接的部分
- 如果文档中有“前后”按钮，则应该把它放到 nav 元素中
- `<nav><a href="index.asp">Home</a></nav>`

## `<noscript>`

- 用来定义在脚本未被执行时的替代内容（文本）
- `<noscript>Your browser does not support JavaScript!</noscript>`

## `<embed />`

- 定义嵌入的内容，比如插件
- 属性
  - width：设置嵌入内容的宽度
  - height：设置嵌入内容的高度
  - src：嵌入内容的 URL
  - type: 定义嵌入内容的类型

```html
<embed src="helloworld.swf" />
```

## `<param>`

- param 元素允许为插入 XHTML 文档的对象规定 run-time 设置, 即此标签可为包含它的 object 标签提供参数
- 属性
  - name：定义参数的唯一的名称（唯一标识符）
  - value：规定参数的值

## `<object>`

- 定义一个嵌入的对象。请使用此元素向 XHTML 页面添加多媒体
- 此元素运行你规定插入 HTML 文档中的对象的数据和参数，以及可用来显示和操作数据的代码
- 标签用于包含对象，比如图像、音频、视频、Java applets、ActiveX、PDF 以及 Flash
- object 元素可位于在 head 元素或 body 元素内部
- `<object>` 与 `</object>` 之间的文本是替换文本，针对不支持此标签的浏览器，通过这种方式，我们能够嵌套多个 object 元素（每个对应一个浏览器）
  - 由于IE只支持对Object的解析，其他浏览器只支持对Embed的解析，所以使用Object嵌套Embed的写法即可
- 至于图像，请使用 `<img>` 标签代替 `<object>` 标签
- 至少必须定义 "data" 和 "type" 属性之一
- 属性
  - data：规定对象使用的资源的 URL
  - type：定义 data 属性中规定的数据的 MIME 类型
  - form：规定对象所属的一个或多个表单, 值是form的id
  - width：规定对象的宽度
  - height：规定对象的高度
  - name：为对象定义名称
  - usemap：规定与对象一同使用的客户端图像映射的名称

```html
<!-- Embed a flash movie -->
<object data="move.swf" type="application/x-shockwave-flash"></object>

<!-- Embed a flash movie with parameters -->
<object data="move.swf" type="application/x-shockwave-flash">
  <param name="foo" value="bar">
</object>

<!-- 嵌入pdf -->
<object type="application/pdf"
    data="/media/examples/In-CC0.pdf"
    width="250"
    height="200">
</object>
```

## `optgroup`

- 定义选项组, 允许你组合 select 里的选项
- 属性
  - label：定义选项组的标注
  - disabled：其加载时，禁用该选项组

```html
<!-- 下拉选项中会显示分类 ‘Swedish Cars’ 和 ‘German Cars’，可选项依旧是 option -->
<select>
    <optgroup label="Swedish Cars">
        <option value ="volvo">Volvo</option>
        <option value ="saab">Saab</option>
    </optgroup>

    <optgroup label="German Cars">
        <option value ="mercedes">Mercedes</option>
        <option value ="audi">Audi</option>
    </optgroup>
</select>
```

## `<pre>`

- 定义预格式化的文本, 被包围在 pre 元素中的文本通常会保留空格和换行符。而文本也会呈现为等宽字体
- pre 标签的一个常见应用就是用来表示计算机的源代码
- 可以导致段落断开的标签(如 p、h1-h6、address)绝不能包含在 pre 所定义的块里，因为换行行为在所有浏览器上不是一样的，同样不推荐使用tab制表符，尽量用空格代替
- pre 元素中允许的文本可以包括物理样式和基于内容的样式变化，还有链接、图像和水平分隔线
- 如果使用 pre 标签来定义计算机源代码，比如 HTML 源代码，请使用符号实体来表示特殊字符，比如 "&lt;" 代表 "<"，"&gt;" 代表 ">"，"&amp;" 代表 "&"
- 常与 code 标签一起使用

## `<progress>`

- 定义运行中的进度（进程）
- 可以使用 progress 标签来显示 JavaScript 中耗费时间的函数的进度
- progress 标签不适合用来表示度量衡（例如，磁盘空间使用情况或查询结果）。如需表示度量衡，请使用 meter 标签(IE不支持)代替
- 属性
  - max：定义完成的值
  - value: 定义进程的当前值

```html
<progress value="22" max="100"></progress>
```

## `<meter>`

- IE不支持
- 定义已知范围或分数值内的标量测量。也被称为 gauge（尺度）
- 磁盘用量、查询结果的相关性，等等
- meter 标签不应用于指示进度（在进度条中）。如果标记进度条，请使用 progress 标签
- 属性
  - form：规定 meter 元素所属的一个或多个表单，值是form的id
  - high：规定被视作高的值的范围
  - low：规定被视作低的值的范围
  - max：规定范围的最大值
  - min：规定范围的最小值
  - optimum：规定度量的优化值，浮点数
  - value：必需。规定度量的当前值
- high、low、max、min 把 meter 分为四个区域，如果 optimum 和 value 在一个区域，则显示蓝色，反之，显示黄色

## `<ruby>`

- 定义 ruby 注释（中文注音或字符）
- 在东亚使用，显示的是东亚字符的发音
- ruby 元素由一个或多个字符（需要一个解释/发音）和一个提供该信息的 rt 元素组成，还包括可选的 rp 元素，定义当浏览器不支持 "ruby" 元素时显示的内容

```html
<ruby>
  <!-- rt信息以注音形式放在 漢 上方 -->
  漢 <rt> ㄏㄢˋ </rt>
</ruby>
```

## `<rt>`

- 定义字符（中文注音或字符）的解释或发音

## `<section>`

- 定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分
- 属性
  - cite：section 的 URL，假如 section 摘自 web 的话

## `<small>`

- 将旁注 (side comments) 呈现为小型文本
- 免责声明、注意事项、法律限制或版权声明的特征通常都是小型文本。小型文本有时也用于新闻来源、许可要求
- 对于由 em 元素强调过的或由 strong 元素标记为重要的文本，small 元素不会取消对文本的强调，也不会降低这些文本的重要性
- 如果被包围的字体已经是字体模型所支持的最小字号，那么 small 标签将不起任何作用
- small 标签也可以嵌套，从而连续地把文字缩小。每个 small 标签都把文本的字体变小一号，直到达到下限的一号字

## `<sub>` 和 `<sup>`

- sub：下标
- sup：上标
- 包含在标签中的内容都会以当前文本流中字符高度的一半来显示，但是与当前文本流中文字的字体和字号都是一样的
- 在数学等式、科学符号和化学公式中都非常有用

## `<textarea>`

- 定义一个文本区域 (text-area) （一个多行的文本输入区域）
- 可以通过 cols 和 rows 属性来规定 textarea 的尺寸，不过更好的办法是使用 CSS 的 height 和 width 属性
- 在文本输入区内的文本行间，用 "%OD%OA" （回车/换行）进行分隔
- 属性
  - autofocus：规定在页面加载后文本区域自动获得焦点
    - autofocus
  - cols：规定文本区内的可见宽度
  - rows: 规定文本区内的可见行数
  - disabled: 规定禁用该文本区
    - disabled
  - form: 规定文本区域所属的一个或多个表单, 值是form的id
  - maxlength: 规定文本区域的最大字符数
  - name：规定文本区的名称
  - placeholder：规定描述文本区域预期值的简短提示
  - readonly：规定文本区为只读
    - readonly
  - required：规定文本区域是必填的
    - required
  - wrap：规定当在表单中提交时，文本区域中的文本如何换行
    - hard、soft

## `<audio>`

- 定义声音，比如音乐或其他音频流
- 标签内的内容只会在浏览器不识别 audio 标签时显示
- 属性
  - autoplay：音频在就绪后马上播放
    - autoplay
  - controls：向用户显示控件，比如播放按钮
    - controls
  - muted：规定视频输出应该被静音
    - muted
  - loop：每当音频结束时重新开始播放
    - loop
  - preload：音频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性
    - preload
  - src：要播放的音频的 URL

## `<video>`

- 定义视频，比如电影片段或其他视频流
- 标签内的内容只会在浏览器不识别 video 标签时显示
- 属性
  - width：设置视频播放器的宽度
  - height：设置视频播放器的高度
  - poster：规定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像
  - autoplay：视频在就绪后马上播放
    - autoplay
  - controls：向用户显示控件，比如播放按钮
    - controls
  - muted：规定视频输出应该被静音
    - muted
  - loop：当媒介文件完成播放后再次开始播放
    - loop
  - preload：视频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性
    - preload
  - src：要播放的视频的 URL

## `<source>`

- 为媒介元素（比如 video 和 audio）定义媒介资源
- 允许你规定可替换的视频/音频文件供浏览器根据它对媒体类型或者编解码器的支持进行选择
- 属性
  - media：规定媒体资源的类型
  - type：规定媒体资源的 MIME 类型
  - src：规定媒体文件的 URL

```html
<!-- 这两份源文件，浏览器会选择它所支持的文件，没有的话打印文本 -->
<audio controls>
   <source src="horse.ogg" type="audio/ogg">
   <source src="horse.mp3" type="audio/mpeg">
 Your browser does not support the audio element.
</audio>
```

## `<form>`

- 用于为用户输入创建 HTML 表单, 用于向服务器传输数据
- 表单能够包含 input 元素，比如文本字段、复选框、单选框、提交按钮等等
- 表单还可以包含 menus、textarea、fieldset、legend 和 label 元素
- form 元素是块级元素，其前后会产生折行
- 属性
  - accept-charset：规定服务器可处理的表单数据字符集
    - 以下为常用值, 如需规定一个以上的字符集，用逗号分隔
      - UTF-8：Unicode 字符编码
      - ISO-8859-1：拉丁字母表的字符编码
      - gb2312：简体中文字符集
  - action：规定当提交表单时向何处发送表单数据
    - 如果表单是发送邮件，则 action 属性值格式为 'MAILTO:' + 对应地址， method 依旧是 post
  - autocomplete：规定是否启用表单的自动完成功能（第一次提交数据后，重载页面即会出现输入提示）
    - on、off
  - enctype：规定在发送表单数据之前如何对其进行编码
    - application/x-www-form-urlencoded：默认值，在发送前编码所有字符（空格转换为 "+" 加号，特殊符号转换为 ASCII HEX 值）
    - multipart/form-data：不对字符编码（在使用包含文件上传控件的表单时，必须使用该值）
    - text/plain：空格转换为 "+" 加号，但不对特殊字符编码
  - method：规定用于发送 form-data 的 HTTP 方法
    - get、post
  - name：规定表单的名称
  - novalidate：如果使用该属性，则提交表单时不进行验证
    - novalidate
  - target：规定在何处打开 action URL
    - _blank、_self、_parent、_top、frame的name值

## `<input>`

- 用于搜集用户信息
- 属性
  - accept：规定通过文件上传来提交的文件的类型, 值是用逗号隔开的 MIME 类型列表
    - 请避免使用该属性。应该在服务器端验证文件上传
    - accept 属性只能与 `<input type="file">` 配合使用
  - autofocus: 规定输入字段在页面加载时是否获得焦点
    - 不适用于 type="hidden"
  - form: 规定输入字段所属的一个或多个表单
    - 值为 form 的 name
  - formaction、formenctype、formmethod、formtarget 均是用来覆盖表单的对应属性
    - 适用于 type="submit" 和 type="image"
  - formnovalidate: 覆盖表单的 novalidate 属性
    - formnovalidate
  - width 和 height 定义 input 字段的宽和高
    - 适用于 type="image"
    - 值的单位均是 px 或 %
  - alt：定义图像输入的替代文本
    - 适用于 type="image"
  - src：定义以提交按钮形式显示的图像的 URL
    - 适用于 type="image"
  - list：引用包含输入字段的预定义选项的 datalist
    - 值是 datalist 的 id
  - min 和 max 规定输入字段的最小值和最大值，创建合法值的范围
    - 值的类型是 number | date
  - step：规定输入字的的合法数字间隔
    - 可以与 min 和 max 配合，创建合法值的范围
  - checked: 规定此 input 元素首次加载时应当被选中
    - checked
  - disabled: 当 input 元素加载时禁用此元素
    - disabled
  - multiple：如果使用该属性，则允许一个以上的值
    - multiple
  - readonly：规定输入字段为只读
    - readonly
  - required：指示输入字段的值是必需的
    - required
  - pattern：规定输入字段的值的模式或格式
    - 值是正则表达式
  - autocomplete: 规定是否使用输入字段的自动完成功能
    - on、off
  - maxlength：规定输入字段中的字符的最大长度
  - placeholder：规定帮助用户填写输入字段的提示
  - size：定义输入字段的宽度
  - name：定义 input 元素的名称
  - value：规定 input 元素的值
  - type：规定 input 元素的类型
    - button：定义可点击按钮
    - hidden：定义隐藏的输入字段
    - password: 定义密码字段。该字段中的字符被掩码
    - text: 定义单行的输入字段，用户可在其中输入文本。默认宽度为 20 个字符
    - radio: 定义单选按钮
    - checkbox: 定义复选框
    - image: 定义图像形式的提交按钮
    - submit: 定义提交按钮。提交按钮会把表单数据发送到服务器
    - reset: 定义重置按钮。重置按钮会清除表单中的所有数据
    - file: 定义输入字段和 "浏览"按钮，供文件上传
  
## Audio DOM对象

- 访问对象：`var x = document.getElementById("myAudio")`
- 创建对象：`var x = document.createElement("AUDIO")`
- 对象属性
  - autoplay: 设置或返回是否在就绪（加载完成）后随即播放音频
  - crossOrigin：设置或返回音频的 CORS 设置
  - currentTime：设置或返回音频中的当前播放位置（以秒计）
  - defaultMuted：设置或返回音频默认是否静音
  - muted：设置或返回是否关闭声音
  - defaultPlaybackRate：设置或返回音频的默认播放速度
  - playbackRate：设置或返回音频播放的速度
  - loop：设置或返回音频是否应在结束时再次播放
  - mediaGroup：设置或返回音频所属媒介组合的名称
  - paused：设置或返回音频是否暂停
  - volume：设置或返回音频的音量
  - preload：设置或返回音频的 preload 属性的值
  - src：设置或返回音频的 src 属性的值
  - controls：设置或返回音频是否应该显示控件（比如播放/暂停等）
  - controller：返回表示音频当前媒体控制器的 MediaController 对象
  - audioTracks: 返回表示可用音频轨道的 AudioTrackList 对象
  - textTracks：返回表示可用文本轨道的 TextTrackList 对象
  - buffered: 返回表示音频已缓冲部分的 TimeRanges 对象
  - played：返回表示音频已播放部分的 TimeRanges 对象
  - error：返回表示音频错误状态的 MediaError 对象
  - seekable：返回表示音频可寻址部分的 TimeRanges 对象
  - seeking：返回用户当前是否正在音频中进行查找
  - currentSrc：返回当前音频的 URL
  - duration：返回音频的长度（以秒计）
  - ended：返回音频的播放是否已结束
  - networkState：返回音频的当前网络状态
  - readyState：返回音频当前的就绪状态
- 对象方法：
  - addTextTrack()：向音频添加新的文本轨道
  - canPlayType()：检查浏览器是否能够播放指定的音频类型, 需要传类似于'audio/wav'的字符串参数进行判断,返回字符串判断说明
  - **getStartDate()**：返回新的 Date 对象，表示当前时间线偏移量
  - **fastSeek()**：在音频播放器中指定播放时间
  - load()：重新加载音频元素
  - play()：开始播放音频
  - pause()：暂停当前播放的音频

## Video DOM对象

- 访问对象：`var x = document.getElementById("myVideo")`
- 创建对象：`var x = document.createElement("VIDEO")`
- 对象属性
  - **poster**：设置或返回视频的 poster 属性的值
  - **width**：设置或返回视频的 width 属性的值
  - **height**：设置或返回视频的 height 属性的值
  - autoplay：设置或返回是否在就绪（加载完成）后随即播放视频
  - crossOrigin：设置或返回视频的 CORS 设置
  - currentTime：设置或返回视频中的当前播放位置（以秒计）
  - defaultMuted：设置或返回视频默认是否静音
  - muted：设置或返回是否关闭声音
  - defaultPlaybackRate：设置或返回视频的默认播放速度
  - playbackRate：设置或返回视频播放的速度
  - loop：设置或返回视频是否应在结束时再次播放
  - mediaGroup：设置或返回视频所属媒介组合的名称
  - paused：设置或返回视频是否暂停
  - volume：设置或返回视频的音量
  - preload：设置或返回视频的 preload 属性的值
  - src：设置或返回视频的 src 属性的值
  - controls：设置或返回视频是否应该显示控件（比如播放/暂停等）
  - controller：返回表示视频当前媒体控制器的 MediaController 对象
  - **startDate**：返回表示当前时间偏移的 Date 对象
  - **videoTracks**：返回表示可用视频轨道的 VideoTrackList 对象
  - audioTracks：返回表示可用音频轨道的 AudioTrackList 对象
  - textTracks：返回表示可用文本轨道的 TextTrackList 对象
  - buffered：返回表示视频已缓冲部分的 TimeRanges 对象
  - played：返回表示视频已播放部分的 TimeRanges 对象
  - error：返回表示视频错误状态的 MediaError 对象
  - seekable：返回表示视频可寻址部分的 TimeRanges 对象
  - seeking：返回用户当前是否正在视频中进行查找
  - currentSrc：返回当前视频的 URL
  - duration：返回视频的长度（以秒计）
  - ended：返回视频的播放是否已结束
  - networkState：返回视频的当前网络状态
  - readyState：返回视频当前的就绪状态
- 对象方法：
  - addTextTrack()：向音频添加新的文本轨道
  - canPlayType()：检查浏览器是否能够播放指定的音频类型
  - load()：重新加载音频元素
  - play()：开始播放音频
  - pause()：暂停当前播放的音频

## Media 事件

- 由媒介（比如视频、图像和音频）触发的事件（适用于所有 HTML 元素，但常见于媒介元素中，比如 `<audio>`、`<embed>`、`<img>`、`<object>` 以及 `<video>`）
  - abort：在退出时。
  - canplay：当文件就绪可以开始播放时（缓冲已足够开始时）
  - canplaythrough：当媒介能够无需因缓冲而停止即可播放至结尾时
  - durationchange：当媒介长度改变时
  - emptied：当发生故障并且文件突然不可用时（比如连接意外断开时）
  - ended：当媒介已到达结尾时（可发送类似“感谢观看”之类的消息）
  - error：当在文件加载期间发生错误时
  - loadeddata：当媒介数据已加载时
  - loadedmetadata：当元数据（比如分辨率和时长）被加载时
  - loadstart：在文件开始加载且未实际加载任何数据前
  - pause：当媒介被用户或程序暂停时
  - play：当媒介已就绪可以开始播放时
  - playing：当媒介已开始播放时
  - progress：当浏览器正在获取媒介数据时
  - ratechange：每当回放速率改变时（比如当用户切换到慢动作或快进模式）
  - readystatechange：每当就绪状态改变时（就绪状态监测媒介数据的状态）
  - seeked：当 seeking 属性设置为 false（指示定位已结束）时
  - seeking：当 seeking 属性设置为 true（指示定位是活动的）时
  - stalled：在浏览器不论何种原因未能取回媒介数据时
  - suspend：在媒介数据完全加载之前不论何种原因终止取回媒介数据时
  - timeupdate：当播放位置改变时（比如当用户快进到媒介中一个不同的位置时）
  - volumechange：每当音量改变时（包括将音量设置为静音）时
  - waiting：当媒介已停止播放但打算继续播放时（比如当媒介暂停以缓冲更多数据）
