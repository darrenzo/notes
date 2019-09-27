# art-template

- 是一个简约、超快的模板引擎
- 它采用作用域预声明的技术来优化模板渲染速度，从而获得接近 JavaScript 极限的运行性能，并且同时支持 NodeJS 和浏览器
- 拥有接近 JavaScript 渲染极限的的性能
- 调试友好：语法、运行时错误日志精确到模板所在行；支持在模板文件上打断点（Webpack Loader）
- 支持 Express、Koa、Webpack
- 支持模板继承与子模板
- 浏览器版本仅 6KB 大小

## 安装

- npm

```shell
npm install art-template --save
```

- 在浏览器中实时编译
  - [template-web.js](https://unpkg.com/art-template/lib/template-web.js)
  - IE8(需要[补丁](https://github.com/aui/art-template/blob/master/example/web-ie-compatible/index.html)才能运行)
  - 兼容IE8+
- 差异
  - 因为浏览器不支持文件系统，所以 template(filename, data) 不支持传入文件路径，它内部使用 document.getElementById(filename).innerHTML 来获取模板

  ```html
  <!-- 例如 -->
  <script src="lib/template-web.js"></script>
  <script id="tpl-user" type="text/html">
  {{if user}}
    <h2>{{user.name}}</h2>
  {{/if}}
  </script>
  ```

- 在浏览器中预编译
  - 使用 Webpack 的 Loader: [art-template-loader](https://aui.github.io/art-template/zh-cn/webpack)
- 插件
  - Webpack: [art-template-loader](https://aui.github.io/art-template/zh-cn/webpack)
  - Express: [express-art-template](https://aui.github.io/art-template/zh-cn/express)
  - Koa: [koa-art-template](https://aui.github.io/art-template/zh-cn/koa)

## 模板

- 支持两种语法，标准语法可以让模板更容易读写；原始语法具有强大的逻辑处理能力

```html
<!-- 标准语法 -->
{{if user}}
  <h2>{{user.name}}</h2>
{{/if}}

<!-- 原始语法 -->
<!-- 原始语法兼容 EJS、Underscore、LoDash 模板 -->
<% if (user) { %>
  <h2><%= user.name %></h2>
<% } %>
```

## 渲染模板

```js
var template = require('art-template');
var html = template(__dirname + '/tpl-user.art', {
    user: {
        name: 'aui'
    }
});
```

## 核心方法

```js
// 基于模板名渲染模板
template(filename, data);

// 将模板源代码编译成函数
template.compile(source, options);

// 将模板源代码编译成函数并立刻执行
template.render(source, data, options);
```

## 语法

- 标准语法支持基本模板语法以及基本 JavaScript 表达式
- 原始语法支持任意 JavaScript 语句，这和 EJS 一样

### 输出

```html
<!-- 标准语法 -->
{{value}}
{{data.key}}
{{data['key']}}
{{a ? b : c}}
{{a || b}}
{{a + b}}

<!-- 原始语法 -->
<%= value %>
<%= data.key %>
<%= data['key'] %>
<%= a ? b : c %>
<%= a || b %>
<%= a + b %>
```

### 原文输出

- 原文输出语句不会对 HTML 内容进行转义处理，可能存在安全风险，请谨慎使用

```html
<!-- 标准语法 -->
{{@ value }}

<!-- 原始语法 -->
<%- value %>
```

## 变量

```html
<!-- 标准语法 -->
{{set temp = data.sub.content}}

<!-- 原始语法 -->
<% var temp = data.sub.content; %>
```

## 控制流

### 条件

```html
<!-- 标准语法 -->
{{if value}} ... {{/if}}
{{if v1}} ... {{else if v2}} ... {{/if}}

<!-- 原始语法 -->
<% if (value) { %> ... <% } %>
<% if (v1) { %> ... <% } else if (v2) { %> ... <% } %>
```

### 循环

```html
<!-- 标准语法 -->
{{each target}}
    {{$index}} {{$value}}
{{/each}}

<!-- 原始语法 -->
<% for(var i = 0; i < target.length; i++){ %>
    <%= i %> <%= target[i] %>
<% } %>
```

## 模板继承

```html
<!-- 标准语法 -->
{{extend './layout.art'}}
{{block 'head'}} ... {{/block}}

<!-- 原始语法 -->
<% extend('./layout.art') %>
<% block('head', function(){ %> ... <% }) %>
```

- 模板继承允许你构建一个包含你站点共同元素的基本模板“骨架”

```html
<!--layout.art-->
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{block 'title'}}My Site{{/block}}</title>

    {{block 'head'}}
    <link rel="stylesheet" href="main.css">
    {{/block}}
</head>
<body>
    {{block 'content'}}{{/block}}
</body>
</html>


<!--index.art-->
{{extend './layout.art'}}

{{block 'title'}}{{title}}{{/block}}

{{block 'head'}}
    <link rel="stylesheet" href="custom.css">
{{/block}}

{{block 'content'}}
<p>This is just an awesome page.</p>
{{/block}}
```

## 子模板

- data 数默认值为 $data；标准语法不支持声明 object 与 array，只支持引用变量，而原始语法不受限制
- art-template 内建 HTML 压缩器，请避免书写 HTML 非正常闭合的子模板，否则开启压缩后标签可能会被意外“优化

```html
<!-- 标准语法 -->
{{include './header.art'}}
{{include './header.art' data}}

<!-- 原始语法 -->
<% include('./header.art') %>
<% include('./header.art', data) %>
```

## 过滤器

- 注册过滤器

```html
template.defaults.imports.dateFormat = function(date, format){};
template.defaults.imports.timestamp = function(value){return value * 1000};
```

- 使用过滤器
  - 过滤器函数第一个参数接受目标值

```html
<!-- 标准语法 -->
<!-- {{value | filter}} 过滤器语法类似管道操作符，它的上一个输出作为下一个输入 -->
{{date | timestamp | dateFormat 'yyyy-MM-dd hh:mm:ss'}}

<!-- 原始语法 -->
<%= $imports.dateFormat($imports.timestamp(date), 'yyyy-MM-dd hh:mm:ss') %>
```

## 调试

- template.defaults.debug
- art-template 内建调试器，能够捕获到语法与运行错误，并且支持自定义的语法
- 在 NodeJS 中调试模式会根据环境变量自动开启：process.env.NODE_ENV !== 'production'

```js
// 设置 template.defaults.debug=true 后，等同于：
{
    "cache": false,
    "minimize": false,
    "compileDebug": true
}
```

## 模板变量

- template.defaults.imports
- 模板通过 $imports 可以访问到模板外部的全局变量与导入的变量

### 导入变量

```js
template.defaults.imports.log = console.log;
```

```html
<% $imports.log('hello world') %>
```

### 内置变量清单

- $data 传入模板的数据
- $imports 外部导入的变量以及全局变量
- print 字符串输出函数
- include 子模板载入函数
- extend 模板继承模板导入函数
- block 模板块声明函数

## 解析规则

- template.defaults.rules
- art-template 可以自定义模板解析规则，默认配置了原始语法与标准语法

### 修改界定符

```js
// 原始语法的界定符规则
template.defaults.rules[0].test = /<%(#?)((?:==|=#|[=-])?)[ \t]*([\w\W]*?)[ \t]*(-?)%>/;

// 标准语法的界定符规则
template.defaults.rules[1].test = /{{([@#]?)[ \t]*(\/?)([\w\W]*?)[ \t]*}}/;
```

- 例如只修改界定符部分

```js
var rule = template.defaults.rules[0];
rule.test = new RegExp(rule.test.source.replace('<%', '<\\\?').replace('%>', '\\\?>'));
```

### 添加语法

- 让模板引擎支持 ES6 ${name} 模板字符串的解析

```js
template.defaults.rules.push({
    test: /\${([\w\W]*?)}/,
    use: function(match, code) {
        return {
            code: code,
            output: 'escape'
        }
    }
});
```

- 其中 test 是匹配字符串正则，use 是匹配后的调用函数。关于 use 函数：
  - 参数：第一个参数为匹配到的字符串，其余的参数依次接收 test 正则的分组匹配内容
  - 返回值：必须返回一个对象，包含 code 与 output 两个字段：
    - code 转换后的 JavaScript 语句
    - output 描述 code 的类型，可选值：
      - 'escape' 编码后进行输出
      - 'raw' 输出原始内容
      - false 不输出任何内容
- 语法规则对渲染速度没有影响，模板引擎编译器会帮你优化渲染性能

## 压缩页面

- template.defaults.minimize = true
- art-template 内建的压缩器可以压缩 HTML、JS、CSS，它在编译阶段运行，因此完全不影响渲染速度，并且能够加快网络传输。

### 配置

- [参考](https://github.com/kangax/html-minifier)

```js
template.defaults.htmlMinifierOptions = {
    collapseWhitespace: true,
    minifyCSS: true,
    minifyJS: true,
    // 运行时自动合并：rules.map(rule => rule.test)
    ignoreCustomFragments: []
};
```

## 选项

- template.defaults

```js
// 模板名
filename: null,

// 模板语法规则列表
rules: [nativeRule, artRule],

// 是否开启对模板输出语句自动编码功能。为 false 则关闭编码输出功能
// escape 可以防范 XSS 攻击
escape: true,

// 启动模板引擎调试模式。如果为 true: {cache:false, minimize:false, compileDebug:true}
debug: detectNode ? process.env.NODE_ENV !== 'production' : false,

// bail 如果为 true，编译错误与运行时错误都会抛出异常
bail: true,

// 是否开启缓存
cache: true,

// 是否开启压缩。它会运行 htmlMinifier，将页面 HTML、CSS、CSS 进行压缩输出
// 如果模板包含没有闭合的 HTML 标签，请不要打开 minimize，否则可能被 htmlMinifier 修复或过滤
minimize: true,

// 是否编译调试版
compileDebug: false,

// 模板路径转换器
resolveFilename: resolveFilename,

// 子模板编译适配器
include: include,

// HTML 压缩器。仅在 NodeJS 环境下有效
htmlMinifier: htmlMinifier,

// HTML 压缩器配置。参见 https://github.com/kangax/html-minifier
htmlMinifierOptions: {
    collapseWhitespace: true,
    minifyCSS: true,
    minifyJS: true,
    // 运行时自动合并：rules.map(rule => rule.test)
    ignoreCustomFragments: []
},

// 错误事件。仅在 bail 为 false 时生效
onerror: onerror,

// 模板文件加载器
loader: loader,

// 缓存中心适配器（依赖 filename 字段）
caches: caches,

// 模板根目录。如果 filename 字段不是本地路径，则在 root 查找模板
root: '/',

// 默认后缀名。如果没有后缀名，则会自动添加 extname
extname: '.art',

// 忽略的变量。被模板编译器忽略的模板变量列表
ignore: [],

// 导入的模板变量
imports: runtime
```

## API

### template(filename, content)

- 根据模板名渲染模板
  - 浏览器版本无法加载外部文件，filename 为存放模板的元素 id
- 参数:
  - {string} filename
  - {Object,string} content
- 返回值：
  - 如果 content 为 Object，则渲染模板并返回 string
  - 如果 content 为 string，则编译模板并返回 function

```js
// 编译模板并缓存
// compile && cache
template('/welcome.art', 'hi, <%=value%>.');

// use
template('/welcome.art', {
    value: 'aui'
});
```

### .compile(source, options)

- 编译模板并返回一个渲染函数
- 参数:
  - {string} filename
  - {Object,string} content
- 返回值：{function}

```js
var render = template.compile('hi, <%=value%>.');
var html = render({value: 'aui'});
```

### .render(source, data, options)

- 编译并返回渲染结果
- 参数:
  - {string} source
  - {Object} options
- 返回值：{string}

```js
var html = template.render('hi, <%=value%>.', {value: 'aui'})
```

### .defaults

- 模板引擎默认配置

### .extension

- 给 NodeJS require.extensions 注册的模板渲染函数
- 类型：{Object}
- 此功能仅对 NodeJS 生效，如果要在浏览器中使用模板文件渲染功能，请使用 Webpack art-template-loader

```js
// 加载.ejs模板
var template = require('art-template');
require.extensions['.ejs'] = template.extension;

var view = require('./index.ejs');
var html = view(data);

// .art 默认被注册，可以直接使用
var template = require('art-template');
var view = require('./index.art');
var html = view(data);
```
