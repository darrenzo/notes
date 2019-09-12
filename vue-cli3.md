# @vue/cli 3.x

- [最新版@vue/cli 3.x](https://cli.vuejs.org/zh/guide/#%E8%AF%A5%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BB%84%E4%BB%B6)
- [旧版vue-cli](https://github.com/vuejs/vue-cli/tree/v2#vue-cli--)

## 安装

- npm install -g @vue/cli 或者 yarn global add @vue/cli
  - 如果你已经全局安装了旧版本的 vue-cli (1.x 或 2.x)，需要先通过 npm uninstall vue-cli -g 或 yarn global remove vue-cli 卸载它
  - 推荐使用Node.js 8.11.0+, 可以使用 nvm 或者 nvm-windows 管理多个node版本
- vue --version 查看版本

## 快速原型开发

- 可以使用 vue serve 和 vue build 命令对单个 *.vue 文件进行快速原型开发
  - vue serve 的缺点就是它需要安装全局依赖，这使得它在不同机器上的一致性不能得到保证
- npm install -g @vue/cli-service-global
- 新建一个App.vue
  
  ```html
  <template>
    <h1>Hello!</h1>
  </template>
  ```

- 在 App.vue 的目录运行 vue serve
  - vue serve 使用了和 vue create 创建的项目相同的默认设置 (webpack、Babel、PostCSS 和 ESLint)
  - 它会在当前目录自动推导入口文件——入口可以是 main.js、index.js、App.vue 或 app.vue 中的一个
  - vue serve MyComponent.vue ：显式地指定入口文件
  - 还可以提供一个 index.html、package.json、安装并使用本地依赖、甚至通过相应的配置文件配置 Babel、PostCSS 和 ESLint
  - 可以使用 vue build 将目标文件构建成一个生产环境的包并用来部署
    - vue build MyComponent.vue
    - vue build 也提供了将组件构建成为一个库或一个 Web Components 组件的能力

## 创建项目

- vue create hello-world
  - 这个命令在 Git Bash 上无效
- 可以选择默认配置或者自行选择需要的设置
  - 在操作提示的最后，可以选择将已选项保存为一个将来可复用的 preset
  - 被保存的 preset 将会存在用户的 home 目录下一个名为 .vuerc 的 JSON 文件里，可编辑
- vue ui : 以图形化界面创建和管理项目
  - 会打开一个浏览器窗口，并以图形化界面将你引导至项目创建的流程

## 插件

- [插件开发指南](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5)
- Vue CLI 使用了一套基于插件的架构
  - 新创建项目的 package.json，依赖都是以 @vue/cli-plugin- 开头的
  - 插件可以修改 webpack 的内部配置，也可以向 vue-cli-service 注入命令。在项目创建的过程中，绝大部分列出的特性都是通过插件来实现的

### 在现有的项目中安装插件

- 每个 CLI 插件都会包含一个 (用来创建文件的) 生成器和一个 (用来调整 webpack 核心配置和注入命令的) 运行时插件
  - 当你使用 vue create 来创建一个新项目的时候，有些插件会根据你选择的特性被预安装好
- 如果你想在一个已经被创建好的项目中安装一个插件，可以使用 vue add 命令
  
  ```shell
  # 这个命令将 @vue/eslint 解析为完整的包名 @vue/cli-plugin-eslint，然后从 npm 安装它，调用它的生成器
  # 等价于 vue add @vue/cli-plugin-eslint
  vue add @vue/eslint

  # 可以向被安装的插件传递生成器选项 (这样做会跳过命令提示)
  vue add @vue/eslint --config airbnb --lintOn save

  # 如果不带 @vue 前缀，该命令会换作解析一个 unscoped 的包
  # 安装并调用第三方插件 vue-cli-plugin-apollo
  vue add apollo

  # 也可以基于一个指定的 scope 使用第三方插件
  # 等价于 vue add @foo/vue-cli-plugin-bar
  vue add @foo/bar

  # vue-router 和 vuex 的情况比较特殊——它们并没有自己的插件，但是依旧可以用vue add
  vue add router
  vue add vuex
  ```

  - vue add 的设计意图是为了安装和调用 Vue CLI 插件。这不意味着替换掉普通的 npm 包。对于这些普通的 npm 包，你仍然需要选用包管理器
  - 推荐在运行 vue add 之前将项目的最新状态提交，因为该命令可能调用插件的文件生成器并很有可能更改你现有的文件
- 如果一个插件已经被安装，你可以使用 vue invoke 命令跳过安装过程，只调用它的生成器
  - 这个命令会接受和 vue add 相同的参数
- 如果插件列在了该项目之外的其它 package.json 文件里，你可以在自己项目的 package.json 里设置 vuePlugins.resolveFrom 选项指向包含其它 package.json 的文件夹

```js
// 如果你有一个 .config/package.json 文件
{
  "vuePlugins": {
    "resolveFrom": ".config"
  }
}
```

### 项目本地的插件

- 如果你需要在项目里直接访问插件 API 而不需要创建一个完整的插件，你可以在 package.json 文件中使用 vuePlugins.service 选项 ？？？？？

```js
// 每个文件都需要暴露一个函数，接受插件 API 作为第一个参数
{
  "vuePlugins": {
    "service": ["my-commands.js"]
  }
}

// 也可以通过 vuePlugins.ui 选项添加像 UI 插件一样工作的文件  ？？？？？？
{
  "vuePlugins": {
    "ui": ["my-ui.js"]
  }
}
```

## Preset

```json
// Preset 的数据会被插件生成器用来生成相应的项目文件
{
  "useConfigFiles": true,
  "router": true,
  "vuex": true,
  "cssPreprocessor": "sass",
  "plugins": {
    "@vue/cli-plugin-babel": {
        // 可以指定插件的版本，不指定则自动使用最新的版本
        // 推荐给所有第三方插件提供显式的版本范围
        "version": "^2.0.0",
        // ... 该插件的其它选项
    },
    "@vue/cli-plugin-eslint": {
      "config": "airbnb",
      "lintOn": ["save", "commit"]
    }
  }
}
```

- 可以为集成工具添加配置，这些额外的配置将会根据 useConfigFiles 的值被合并到 package.json 或相应的配置文件中

```json
// 当 "useConfigFiles": true 的时候，configs 的值将会被合并到 vue.config.js 中
{
  "useConfigFiles": true,
  "plugins": {...},
  "configs": {
    "vue": {...},
    "postcss": {...},
    "eslintConfig": {...},
    "jest": {...}
  }
}
```

### 允许插件的命令提示

- 每个插件在项目创建的过程中都可以注入它自己的命令提示，不过当你使用了一个 preset，这些命令提示就会被跳过，因为 Vue CLI 假设所有的插件选项都已经在 preset 中声明过了
- 如果希望 preset 只声明需要的插件，同时让用户通过插件注入的命令提示来保留一些灵活性，可以使用`"prompts": true`

```json
{
  "plugins": {
    "@vue/cli-plugin-eslint": {
      // 让用户选取他们自己的 ESLint config
      "prompts": true
    }
  }
}
```

### 远程 Preset

- 可以通过发布 git repo 将一个 preset 分享给其他开发者
- repo 包含以下文件：
  - preset.json: 包含 preset 数据的主要文件（必需）
  - generator.js: 一个可以注入或是修改项目中文件的 Generator
  - prompts.js 一个可以通过命令行对话为 generator 收集选项的 prompts 文件
- 发布 repo 后，你就可以在创建项目的时候通过 --preset 选项使用这个远程的 preset 了
  
  ```shell
  # 从 GitHub repo 使用 preset
  vue create --preset username/repo my-project

  # 从私有 repo 获取，请确保使用 --clone 选项
  vue create --preset gitlab:username/repo --clone my-project
  vue create --preset bitbucket:username/repo --clone my-project
  ```

### 加载本地文件系统里的Preset （本地测试 preset）

- 如果 --preset 选项的值是一个相对或绝对文件路径，或是以 .json 结尾，则 Vue CLI 会加载本地的 preset

```shell
# ./my-preset 应当是一个包含 preset.json 的文件夹
vue create --preset ./my-preset my-project

# 或者，直接使用当前工作目录下的 json 文件：
vue create --preset my-preset.json my-project
```

## CLI 服务

- 通过 Vue CLI 创建的项目让你无需 eject 就能够配置工具的几乎每个角落

### vue-cli-service serve

- 在一个 Vue CLI 项目中，@vue/cli-service 安装了一个名为 vue-cli-service 的命令
  - 可以在 npm scripts 中以 vue-cli-service、或者从终端中以 ./node_modules/.bin/vue-cli-service 访问这个命令
- vue-cli-service serve 命令会启动一个开发服务器 (基于 webpack-dev-server) 并附带开箱即用的模块热重载
- vue-cli-service serve 命令行参数
  - 命令行参数 [entry] 将被指定为唯一入口，而非额外的追加入口  ？？？？？
  - 尝试使用 [entry] 覆盖 config.pages 中的 entry 将可能引发错误 ？？？？？
  - 除了通过命令行参数，也可以使用 **vue.config.js** 里的 **devServer** 字段配置开发服务器

```text
用法：vue-cli-service serve [options] [entry]

选项：

  --open    在服务器启动时打开浏览器
  --copy    在服务器启动时将 URL 复制到剪切版
  --mode    指定环境模式 (默认值：development)
  --host    指定 host (默认值：0.0.0.0)
  --port    指定 port (默认值：8080)
  --https   使用 https (默认值：false)
```

### vue-cli-service build

- 命令行参数

```text
用法：vue-cli-service build [options] [entry|pattern]

选项：

  --mode        指定环境模式 (默认值：production)
  --dest        指定输出目录 (默认值：dist)
  --modern      使用现代模式构建应用，为现代浏览器交付原生支持的 ES2015 代码，并生成一个兼容老浏览器的包用来自动回退
  --target      app | lib | wc | wc-async (默认值：app)
  --name        库或 Web Components 模式下的名字 (默认值：package.json 中的 "name" 字段或入口文件名)
  --no-clean    在构建项目之前不清除目标目录
  --report      生成 report.html 以帮助分析包内容
  --report-json 生成 report.json 以帮助分析包内容
  --watch       监听文件变化
```

- vue-cli-service build 会在 dist/ 目录产生一个可用于生产环境的包，带有 JS/CSS/HTML 的压缩，和为更好的缓存而做的自动的 vendor chunk splitting。它的 chunk manifest 会内联在 HTML 里

### vue-cli-service inspect

- 审查一个 Vue CLI 项目的 **webpack config**

```text
用法：vue-cli-service inspect [options] [...paths]

选项：

  --mode    指定环境模式 (默认值：development)
```

### 查看所有的可用命令

- 有些 CLI 插件会向 vue-cli-service 注入额外的命令
  - @vue/cli-plugin-eslint 会注入 vue-cli-service lint 命令

```shell
# 查看所有注入的命令
npx vue-cli-service help

# 查看每个命令可用的选项
npx vue-cli-service help [command]
```

### 缓存与并行处理

- **cache-loader** 会默认为 Vue/Babel/TypeScript 编译开启
  - 文件会缓存在 node_modules/.cache 中
  - 如果遇到了编译方面的问题，可以先删掉缓存目录之后再试试看
- **thread-loader** 会在多核 CPU 的机器上为 Babel/TypeScript 转译开启

### Git Hook

- 在安装之后，@vue/cli-service 也会安装 [yorkie](https://github.com/yyx990803/yorkie)，它会让你在 package.json 的 gitHooks 字段中方便地指定 Git hook
  - yorkie fork 自 [husky](https://github.com/typicode/husky) 且并不和之后的版本兼容

```json
{
  "gitHooks": {
    "pre-commit": "lint-staged"
  }
}
```

## 浏览器兼容性

### browserslist

- [取值范围](https://github.com/ai/browserslist)
- 默认值选项 "browserslist": ["defaults"]
- package.json 文件里的 browserslist 字段 (或一个单独的 .browserslistrc 文件)，指定了项目的目标浏览器的范围
  - 这个值会被 @babel/preset-env 和 Autoprefixer 用来确定需要转译的 JavaScript 特性和需要添加的 CSS 浏览器前缀

### Polyfill

- 一个默认的 Vue CLI 项目会使用 **@vue/babel-preset-app**，它通过 **@babel/preset-env** 和 **browserslist** 配置来决定项目需要的 polyfill
  - 默认情况下，它会把 **useBuiltIns: 'usage'** 传递给 **@babel/preset-env**，这样它会根据源代码中出现的语言特性自动检测需要的 polyfill。这确保了最终包里 polyfill 数量的最小化
  - 注意：如果其中一个依赖需要特殊的 polyfill，默认情况下 Babel 无法将其检测出来
- 有依赖需要 polyfill 时的三种情况
  1. 如果该依赖基于一个目标环境不支持的 ES 版本撰写
     - 将该依赖添加到 vue.config.js 中的 transpileDependencies 选项。这会为该依赖同时开启语法转换和根据使用情况检测 polyfill
  2. 如果该依赖交付了 ES5 代码并显式地列出了需要的 polyfill
     - 可以使用 **@vue/babel-preset-app** 的 polyfills 选项预包含所需要的 polyfill
     - 注意 **es6.promise** 将被默认包含，因为现在的库依赖 Promise 是非常普遍的
     - 推荐以这种方式添加 polyfill 而不是在源代码中直接导入它们，因为如果这里列出的 polyfill 在 browserslist 的目标中不需要，则它会被自动排除

     ```js
     // babel.config.js
     module.exports = {
       presets: [
         ['@vue/app', {
             polyfills: [
               'es6.promise',
               'es6.symbol'
             ]
             // useBuiltIns在此设置，默认值是usage，与babel原生的默认值不一样
         }]
       ]
     }
     ```

  3. 如果该依赖交付 ES5 代码，但使用了 ES6+ 特性且没有显式地列出需要的 polyfill (例如 Vuetify)
     - 使用 **useBuiltIns: 'entry'** 然后在入口文件添加 **import '@babel/polyfill'**, 这会根据 browserslist 目标导入所有 polyfill, 但是因为包含了一些没有用到的 polyfill 所以最终的包大小可能会增加
- 构建库或是 Web Component 时的 Polyfills
  - 推荐给 **@vue/babel-preset-env** 传入 **useBuiltIns: false** 选项, 这能够确保你的库或是组件不包含不必要的 polyfills

### 现代模式

- 为了兼容旧版浏览器，Babel 转译后的包通常都比原生的 ES2015+ 代码会更冗长，运行更慢
- Vue CLI 会产生两个应用的版本：一个现代版的包，面向支持 ES modules 的现代浏览器，另一个旧版的包，面向不支持的旧浏览器
  - **vue-cli-service build --modern** 为生产环境构建
  - 其生成的 HTML 文件会自动使用 [Phillip Walton 精彩的博文](https://philipwalton.com/articles/deploying-es2015-code-in-production-today/) 中讨论到的技术
    - 现代版的包会通过 `<script type="module">` 在被支持的浏览器中加载；它们还会使用 `<link rel="modulepreload">` 进行预加载。
    - 旧版的包会通过 `<script nomodule>` 加载，并会被支持 ES modules 的浏览器忽略。
    - 一个针对 **Safari 10** 中 `<script nomodule>` 的修复会被自动注入
- `<script type="module">` [需要配合始终开启的 CORS 进行加载](https://jakearchibald.com/2017/es-modules-in-browsers/#always-cors)。这意味着你的服务器必须返回诸如 `Access-Control-Allow-Origin: *` 的有效的 CORS 头
  - 如果你想要通过认证来获取脚本，可使将 **vue.config.js** 的 **crossorigin** 选项设置为 **use-credentials**
- 现代浏览器使用一段内联脚本来避免 Safari 10 重复加载脚本包，所以如果你在使用一套严格的 CSP，你需要这样显性地允许内联脚本：`Content-Security-Policy: script-src 'self' 'sha256-4RS22DYeB7U14dra4KcQYxmwt5HkOInieXK1NUMBmQI='`

## HTML 和 静态资源

### HTML 插值

- 因为 public/index.html 文件被用作模板，所以你可以使用 [lodash template](https://cli.vuejs.org/zh/guide/mode-and-env.html#using-env-variables-in-client-side-code) 语法插入内容
  - <%= VALUE %> 用来做不转义插值
  - <%- VALUE %> 用来做 HTML 转义插值
  - <% expression %> 用来描述 JavaScript 流程控制
- 可以使用被 html-webpack-plugin 暴露的默认值

```js
plugins: [
  new HtmlWebpackPlugin({
    title: 'Custom template using Handlebars',
    template: 'index.hbs'
  })
]

<title><%= htmlWebpackPlugin.options.title %></title>
```

- 可以使用所有客户端环境变量（详见后文）

```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```

### preload-webpack-plugin

- `<link rel="preload">` 是一种 resource hint，用来指定页面加载后很快会被用到的资源, 所以在页面加载的过程中，我们希望在浏览器开始主体渲染之前尽早 preload
  - 默认情况下，一个 Vue CLI 应用会为所有初始化渲染需要的文件自动生成 preload 提示
- `<link rel="prefetch">` 是一种 resource hint，用来告诉浏览器在页面加载完成后，利用空闲时间提前获取用户未来可能会访问的内容
  - 默认情况下，一个 Vue CLI 应用会为所有作为 async chunk 生成的 JavaScript 文件 (通过动态 import() 按需 code splitting 的产物) 自动生成 prefetch 提示
- 这些提示会被 @vue/preload-webpack-plugin 注入，并且可以通过 chainWebpack 的 config.plugin() 进行修改和删除

```js
// vue.config.js
// prefetch 例子
module.exports = {
  chainWebpack: config => {
    // 移除 prefetch 插件
    config.plugins.delete('prefetch')

    // 或者
    // 修改它的选项：
    config.plugin('prefetch').tap(options => {
      options[0].fileBlacklist = options[0].fileBlacklist || []
      options[0].fileBlacklist.push(/myasyncRoute(.)+?\.js$/)
      return options
    })
  }
}
```

- 当 prefetch 插件被禁用时，你可以通过 webpack 的内联注释手动选定要提前获取的代码区块
  - Prefetch 链接将会消耗带宽。如果你的应用很大且有很多 async chunk，而用户主要使用的是对带宽较敏感的移动端，那么你可能需要关掉 prefetch 链接并手动选择要提前获取的代码区块

```js
// webpack 的运行时会在父级区块被加载之后注入 prefetch 链接
import(/* webpackPrefetch: true */ './someAsyncComponent.vue')
```

### 不生成 index

- 当基于已有的后端使用 Vue CLI 时，你可能不需要生成 index.html，这样生成的资源可以用于一个服务端渲染的页面

```js
// vue.config.js
module.exports = {
  // 去掉文件名中的 hash
  filenameHashing: false,
  // 删除 HTML 相关的 webpack 插件
  chainWebpack: config => {
    config.plugins.delete('html')
    config.plugins.delete('preload')
    config.plugins.delete('prefetch')
  }
}
```

- 不推荐这样做
  - 硬编码的文件名不利于实现高效率的缓存控制
  - 硬编码的文件名也无法很好的进行 code-splitting (代码分段)，因为无法用变化的文件名生成额外的 JavaScript 文件
  - 硬编码的文件名无法在现代模式下工作
- 考虑换用 [indexPath](https://cli.vuejs.org/zh/config/#indexpath) 选项将生成的 HTML 用作一个服务端框架的视图模板

### 构建一个多页应用

- Vue CLI 支持使用 vue.config.js 中的 [pages](https://cli.vuejs.org/zh/config/#pages) 选项构建一个多页面的应用
- 构建好的应用将会在不同的入口之间高效共享通用的 chunk 以获得最佳的加载性能

### 处理静态资源

- 在 JavaScript 被导入或在 template/CSS 中通过相对路径被引用。这类引用会被 webpack 处理
- 放置在 public 目录下或通过绝对路径被引用。这类资源将会直接被拷贝，而不会经过 webpack 的处理
- 从相对路径导入
  - 当你在 JavaScript、CSS 或 *.vue 文件中使用相对路径 (必须以 . 开头) 引用一个静态资源时，该资源将会被包含进入 webpack 的依赖图中，资源 URL 都会被解析为一个模块依赖
    - 在其内部，通过 file-loader 用版本哈希值和正确的公共基础路径来决定最终的文件路径，再用 url-loader 将小于 4kb 的资源内联，以减少 HTTP 请求的数量
  
  ```js
  <img src="./image.png">

  // 将会被编译到
  h('img', { attrs: { src: require('./image.png') }})
  ```

  - 可以通过 chainWebpack 调整内联文件的大小限制

  ```js
  // vue.config.js
  module.exports = {
    chainWebpack: config => {
      config.module
        .rule('images')
          .use('url-loader')
            .loader('url-loader')
            .tap(options => Object.assign(options, { limit: 10240 }))

      // 可开启对ico文件的使用，比如cursor: url('1.ico'), default;自定义使用ico图片时
      config.module
        .rule('ico')
        .test(/\.(ico)(\?.*)?$/)
        .use('url-loader')
        .loader('url-loader')
        .end()

      // 为vue中的less 路径设置别名
      config.module
        .rule('less')
        .oneOf('vue')
        .use('css-loader')
          .loader('css-loader')
          .tap(options => Object.assign(options, {
              alias: {
                  '@': path.join(__dirname, './src/renderer')
              }
          }))
      // 使用： background: url(~@/1.ico);
    }
  }
  ```

- 推荐将资源作为你的模块依赖图的一部分导入，这样它们会通过 webpack 的处理并获得如下好处：
  - 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求
  - 文件丢失会直接在编译时报错，而不是到了用户端才产生 404 错误
  - 最终生成的文件名包含了内容哈希，因此你不必担心浏览器会缓存它们的老版本
- URL 转换规则(vue-loader进行处理)
  - 如果 URL 是一个绝对路径 (例如 /images/foo.png)，它将会被保留不变
  - 如果 URL 以 **.** 开头，它会作为一个相对模块请求被解释且基于你的文件系统中的目录结构进行解析
  - 如果 URL 以 **~** 开头，其后的任何内容都会作为一个模块请求被解析。这意味着你甚至可以引用 Node 模块中的资源: `<img src="~some-npm-package/foo.png">`
  - 如果 URL 以 **@** 开头，它也会作为一个模块请求被解析。它的用处在于 Vue CLI 默认会设置一个指向 `<projectRoot>/src` 的别名 @。(仅作用于模版中)
- vue-loader 默认下列标签/特性的组合会被转换，且这些组合时可以使用 [transformAssetUrls](https://vue-loader.vuejs.org/zh/options.html#transformasseturls) 选项进行配置的
  
  ```js
  {
    video: ['src', 'poster'],
    source: 'src',
    img: 'src',
    image: ['xlink:href', 'href'],
    use: ['xlink:href', 'href']
  }
  ```

- **public** 文件夹
  - 任何放置在 public 文件夹的静态资源都会被简单的复制，而不经过 webpack
  - 你需要通过绝对路径来引用它们
  - **public** 目录提供的是一个应急手段，当你通过绝对路径引用它时，留意应用将会部署到哪里
    - 如果你的应用没有部署在域名的根部，那么你需要为你的 URL 配置 **publicPath** 前缀：
      - 在 public/index.html 或其它通过 html-webpack-plugin 用作模板的 HTML 文件中，你需要通过 <%= BASE_URL %> 设置链接前缀：`<link rel="icon" href="<%= BASE_URL %>favicon.ico">`
      - 在模板中，你首先需要向你的组件传入基础 URL：

      ```html
      <img :src="`${publicPath}my-image.png`">


      data () {
        return {
          publicPath: process.env.BASE_URL
        }
      }
      ```

    - 何时使用：
      - 你需要在构建输出中指定一个文件的名字
      - 你有上千个图片，需要动态引用它们的路径
      - 有些库可能和 webpack 不兼容，这时你除了将其用一个独立的 `<script>` 标签引入没有别的选择

## CSS

- Vue CLI 项目天生支持 PostCSS、CSS Modules 和包含 Sass、Less、Stylus 在内的预处理器

### 引用

- 所有编译后的 CSS 都会通过 css-loader 来解析其中的 url() 引用，并将这些引用作为模块请求来处理，所以可以根据本地的文件结构用相对路径来引用静态资源
- 如果想要引用一个 npm 依赖中的文件，或是想要用 webpack alias，则需要在路径前加上 ~ 的前缀来避免歧义

### 预处理器

- 可以在创建项目的时候选择预处理器 (Sass/Less/Stylus)。如果当时没有选好，内置的 webpack 仍然会被预配置为可以完成所有的处理
- 也可以手动安装相应的 webpack loader
  - npm install -D sass-loader node-sass
  - npm install -D less-loader less
  - npm install -D stylus-loader stylus

```html
<style lang="scss">
  $color: red;
</style>
```

### 自动化导入

- 如果你想自动化导入文件 (用于颜色、变量、mixin……)，可以使用 vue-cli-plugin-style-resources-loader (或 style-resources-loader)
  - vue项目需要使用 vue add style-resources-loader下载
  - 例如 在每个单文件组件和 Less 文件中导入 ./src/styles/common.less

  ```js
  // vue.config.js
  const path = require('path')
  module.exports = {
    pluginOptions: {
      'style-resources-loader': {
        'preProcessor': 'less',
        'patterns': [
          path.resolve(__dirname, './src/styles/common.less')
        ]
      }
    }
  }
  ```

### PostCSS

- Vue CLI 内部使用了 PostCSS
  - 可以通过 **.postcssrc** 或任何 **postcss-load-config** 支持的配置源来配置 PostCSS
  - 可以通过 **vue.config.js** 中的 **css.loaderOptions.postcss** 配置 **postcss-loader**
- Vue CLI 默认开启了 **autoprefixer**。如果要配置目标浏览器，可使用 package.json 的 [browserslist](https://github.com/ai/browserslist) 字段
- 在生产环境构建中，Vue CLI 会优化 CSS 并基于目标浏览器抛弃不必要的浏览器前缀规则
- 因为默认开启了 autoprefixer，你只使用无前缀的 CSS 规则即可

### CSS Modules

- 不会对元素标签进行处理，无法处理属性选择器等, 推荐使用**scoped**进行完全隔离
- 可以通过 `<style module>` 以开箱即用的方式在 `*.vue` 文件中使用 [CSS Modules](http://www.mamicode.com/info-detail-2427137.html)
- 如果想在 JavaScript 中作为 CSS Modules 导入 CSS 或其它预处理文件，该文件应该以 .module.(css|less|sass|scss|styl) 结尾

```js
import styles from './foo.module.css'
// 所有支持的预处理器都一样工作
import sassStyles from './foo.module.scss'
```

- 如果你想去掉文件名中的 **.module**，可以设置 vue.config.js 中的 css.modules 为 true

```js
// vue.config.js
module.exports = {
  css: {
    modules: true
  }
}
```

- 自定义生成的 CSS Modules 模块的类名, 可以通过 vue.config.js 中的 css.loaderOptions.css 选项来实现。所有的 css-loader 选项在这里都是支持的

```js
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      css: {
        localIdentName: '[name]-[hash]',
        camelCase: 'only'
      }
    }
  }
}
```

### 向预处理器 Loader 传递选项

- 如果想要向 webpack 的预处理器 loader 传递选项，可以使用 vue.config.js 中的 css.loaderOptions 选项

```js
// 向所有样式传入共享的全局变量
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      // 给 sass-loader 传递选项
      sass: {  // 相当于options选项，由vue-loader处理
        // @/ 是 src/ 的别名
        // 所以这里假设你有 `src/variables.scss` 这个文件
        // 在所有被处理的文件之间共享常见的变量，而不需要显式地导入它们, 比如variables中定义的@color等
        // 也可以写成 data: `$color: red;`
        data: `@import "~@/variables.scss";`
      },

      // 给 less-loader 传递选项
      less: {
        globalVars: {
          color: '#ccc'
        }
      },

      // 给 stylus-loader 传递选项
      stylus: {
        import: path.resolve(__dirname, './src/styles/global.styl')
      }
    }
  }
}
```

- Loader 可以通过 loaderOptions 配置，包括：css-loader、postcss-loader、sass-loader、less-loader、stylus-loader
  - 这样做比使用 chainWebpack 手动指定 loader 更推荐，因为这些选项需要应用在使用了相应 loader 的多个地方

## webpack 配置

- 需要熟悉 [webpack-chain 的 API](https://github.com/mozilla-neutrino/webpack-chain#getting-started) 并阅读一些[源码](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-service/lib/config)以便了解如何最大程度利用好这个选项，但是比起直接修改 webpack 配置，它的表达能力更强，也更为安全

### 配置方式

- 调整 webpack 配置最简单的方式就是在 vue.config.js 中的 configureWebpack 选项提供一个对象, 该对象将会被 webpack-merge 合并入最终的 webpack 配置

```js
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```

- 有些 webpack 选项是基于 vue.config.js 中的值设置的，所以不能直接修改
  - 因为 vue.config.js 中的值会被用在配置里的多个地方，以确保所有的部分都能正常工作在一起
  - 例如：
    - 应该修改 vue.config.js 中的 outputDir 选项而不是修改 output.path
    - 应该修改 vue.config.js 中的 publicPath 选项而不是修改 output.publicPath
- 如果需要基于环境有条件地配置行为，或者想要直接修改配置，那就换成一个函数 (该函数会在环境变量被设置之后懒执行)
  - 该方法的第一个参数会收到已经解析好的配置。在函数内，你可以直接修改配置，或者返回一个将会被合并的对象

  ```js
  // vue.config.js
  module.exports = {
    configureWebpack: config => {
      if (process.env.NODE_ENV === 'production') {
        // 为生产环境修改配置...
      } else {
        // 为开发环境修改配置...
      }
    }
  }
  ```

### 链式操作

- Vue CLI 内部的 webpack 配置是通过 webpack-chain 维护的
  - 这个库提供了一个 webpack 原始配置的上层抽象，使其可以定义具名的 loader 规则和具名插件，并有机会在后期进入这些规则并对它们的选项进行修改

#### 修改 Loader 选项

- 对于 CSS 相关 loader 来说，我们推荐使用 css.loaderOptions 而不是直接链式指定 loader。这是因为每种 CSS 文件类型都有多个规则，而 css.loaderOptions 可以确保你通过一个地方影响所有的规则

```js
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
        .loader('vue-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
  }
}
```

#### 添加一个新的 Loader

```js
// vue.config.js
module.exports = {
  chainWebpack: config => {
    // GraphQL Loader
    config.module
      .rule('graphql')
      .test(/\.graphql$/)
      .use('graphql-tag/loader')
        .loader('graphql-tag/loader')
        .end()
  }
}
```

#### 替换一个规则里的 Loader

- 例如：为内联的 SVG 文件使用 **vue-svg-loader** 而不是加载这个文件

```js
// vue.config.js
module.exports = {
  chainWebpack: config => {
    const svgRule = config.module.rule('svg')

    // 清除已有的所有 loader。
    // 如果你不这样做，接下来的 loader 会附加在该规则现有的 loader 之后。
    svgRule.uses.clear()

    // 添加要替换的 loader
    svgRule
      .use('vue-svg-loader')
        .loader('vue-svg-loader')
  }
}
```

#### 修改插件选项

```js
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config
      .plugin('html')
      .tap(args => {
        return [/* 传递给 html-webpack-plugin's 构造函数的新参数 */]
      })
  }
}
```

- 例如：要将 index.html 默认的路径从 /Users/username/proj/public/index.html 改为 /Users/username/proj/app/templates/index.html，可以在下列配置中传入一个新的模板路径来改变它

```js
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config
      .plugin('html')
      .tap(args => {
        args[0].template = '/Users/username/proj/app/templates/index.html'
        return args
      })
  }
}
```

#### 设置别名

```js
const path = require('path')
module.exports = {
  chainWebpack: config => {
    config.resolve.alias
      .set('@', path.join(__dirname, "./src/renderer"))
      .set('@main', path.join(__dirname, "./src/renderer/main"))
  }
}
```

#### 审查项目的 webpack 配置

- @vue/cli-service 对 webpack 配置进行了抽象
- vue-cli-service 暴露了 inspect 命令用于审查解析好的 webpack 配置
- 全局的 vue 可执行程序同样提供了 inspect 命令，这个命令只是简单的把 vue-cli-service inspect 代理到了你的项目中
  - 该命令会将解析出来的 webpack 配置、包括链式访问规则和插件的提示打印到 stdout

```shell
# 可以将其输出重定向到一个文件以便进行查阅
# 注意它输出的并不是一个有效的 webpack 配置文件，而是一个用于审查的被序列化的格式
vue inspect > output.js

# 可以通过指定一个路径来审查配置的一小部分
# 只审查第一条规则
vue inspect module.rules.0

# 指向一个规则或插件的名字
vue inspect --rule vue
vue inspect --plugin html

# 可以列出所有规则和插件的名字
vue inspect --rules
vue inspect --plugins
```

#### 以一个文件的方式使用解析好的配置

- 有些外部工具可能需要通过一个文件访问解析好的 webpack 配置，比如那些需要提供 webpack 配置路径的 IDE 或 CLI。在这种情况下你可以使用如下路径
  - `<projectRoot>/node_modules/@vue/cli-service/webpack.config.js`
  - 该文件会动态解析并输出 vue-cli-service 命令中使用的相同的 webpack 配置，包括那些来自插件甚至是你自定义的配置

## 环境变量和模式

- 你可以替换你的项目根目录中的下列文件来指定环境变量

```shell
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```

- 一个环境文件只包含环境变量的“键=值”对

```shell
# 被载入的变量将会对 vue-cli-service 的所有命令、插件和依赖可用
FOO=bar
VUE_APP_SECRET=secret
```

- 环境加载属性
  - 为一个特定模式准备的环境文件的 (例如 .env.production) 将会比一般的环境文件 (例如 .env) 拥有更高的优先级
  - Vue CLI 启动时已经存在的环境变量拥有最高优先级，并不会被 .env 文件覆写   ？？？？？
- 如果在环境中有默认的 NODE_ENV，你应该移除它或在运行 vue-cli-service 命令的时候明确地设置 NODE_ENV  ？？？？

### 模式

- 默认情况下，一个 Vue CLI 项目有三个模式
  - development 模式用于 vue-cli-service serve
  - production 模式用于 vue-cli-service build 和 vue-cli-service test:e2e
  - test 模式用于 vue-cli-service test:unit
- 注意模式不同于 NODE_ENV，一个模式可以包含多个环境变量
  - 每个模式都会将 NODE_ENV 的值设置为模式的名称——比如在 development 模式下 NODE_ENV 的值会被设置为 "development"
- 可以通过为 .env 文件增加后缀来设置某个模式下特有的环境变量
  - 如果你在项目根目录创建一个名为 .env.development 的文件，那么在这个文件里声明过的变量就只会在 development 模式下被载入
  - 可以通过传递 --mode 选项参数为命令行覆写默认的模式
    - 例如，如果你想要在构建命令中使用开发环境变量，请在你的 package.json 脚本中加入： "dev-build": "vue-cli-service build --mode development"
  - **--mode**的使用
    - 例如： Staging 模式
      - vue-cli-service build 会加载可能存在的 .env、.env.production 和 .env.production.local 文件然后构建出生产环境应用
      - vue-cli-service build --mode staging 会在 staging 模式下加载可能存在的 .env、.env.staging 和 .env.staging.local 文件然后构建出生产环境应用
      - 这两种情况下，根据 **NODE_ENV**，构建出的应用都是生产环境应用，但是在 staging 版本中，process.env.VUE_APP_TITLE 被覆写成了另一个值

    ```shell
    # .env 文件
    VUE_APP_TITLE=My App

    # .env.staging 文件
    NODE_ENV=production
    VUE_APP_TITLE=My App (staging)
    ```

### 在客户端侧代码中使用环境变量

- 只有以 VUE_APP_ 开头的变量会被 webpack.DefinePlugin 静态嵌入到客户端侧的包中

```js
// 可以在应用的代码中这样访问它们
// 在构建过程中，process.env.VUE_APP_SECRET 将会被相应的值所取代
// 例如在 VUE_APP_SECRET=secret 的情况下，它会被替换为 "sercet"
console.log(process.env.VUE_APP_SECRET)
```

- 除了 VUE_APP_* 变量之外，在你的应用代码中始终可用的还有两个特殊的变量
  - NODE_ENV - 会是 "development"、"production" 或 "test" 中的一个。具体的值取决于应用运行的模式
  - BASE_URL - 会和 vue.config.js 中的 publicPath 选项相符，即你的应用会部署到的基础路径
- 所有解析出来的环境变量都可以在 public/index.html 中以 HTML 插值中介绍的方式使用
- 可以在 vue.config.js 文件中计算环境变量，它们仍然需要以 VUE_APP_ 前缀开头
  - 例如用于版本信息 process.env.VUE_APP_VERSION = require('./package.json').version

### 只在本地有效的变量

- 有的时候你可能有一些不应该提交到代码仓库中的变量，尤其是当你的项目托管在公共仓库时
- 应该使用一个 **.env.local** 文件取而代之。本地环境文件默认会被忽略，且出现在 **.gitignore** 中
- **.local**也可以加在指定模式的环境文件上，比如 **.env.development.local** 将会在 development 模式下被载入，且被 git 忽略

## 构建目标

- 当你运行 vue-cli-service build 时，你可以通过 --target 选项指定不同的构建目标
  - 它允许你将相同的源代码根据不同的用例生成不同的构建

### 应用（app）模式

- 应用模式是默认的模式
  - index.html 会带有注入的资源和 resource hint
  - 第三方库会被分到一个独立包以便更好的缓存
  - 小于 4kb 的静态资源会被内联在 JavaScript 中
  - public 中的静态资源会被复制到输出目录中

### 库模式

- IE兼容性问题
  - 在库模式中，项目的 publicPath 是根据主文件的加载路径[动态设置](https://github.com/vuejs/vue-cli/blob/dev/packages/@vue/cli-service/lib/commands/build/setPublicPath.js)的（用以支持动态的资源加载能力）
  - 这个功能用到了 document.currentScript，而 IE 浏览器并不支持这一特性。所以如果网站需要支持 IE 的话，建议使用库之前先在页面上引入 current-script-polyfill
- 注意对 Vue 的依赖
  - 在库模式中，Vue 是外置的。这意味着包中不会有 Vue，即便你在代码中导入了 Vue
  - 如果这个库会通过一个打包器使用，它将尝试通过打包器以依赖的方式加载 Vue；否则就会回退到一个全局的 Vue 变量
- 将一个单独的入口构建为一个库

```shell
# 这个入口可以是一个 .js 或一个 .vue 文件。如果没有指定入口，则会使用 src/App.vue
vue-cli-service build --target lib --name myLib [entry]
```

- 构建一个库会输出
  - dist/myLib.common.js：一个给打包器用的 CommonJS 包 (不幸的是，webpack 目前还并没有支持 ES modules 输出格式的包)
  - dist/myLib.umd.js：一个直接给浏览器或 AMD loader 使用的 UMD 包
  - dist/myLib.umd.min.js：压缩后的 UMD 构建版本
  - dist/myLib.css：提取出来的 CSS 文件 (可以通过在 vue.config.js 中设置 css: { extract: false } 强制内联)
- Vue 和 vs. JS/TS 入口文件   ？？？？？
  - 当使用一个 **.vue** 文件作为入口时，你的库会直接暴露这个 Vue 组件本身，因为组件始终是默认导出的内容
  - 当你使用一个 **.js** 或 **.ts** 文件作为入口时，它可能会包含具名导出，所以库会暴露为一个模块
    - 你的库必须在 UMD 构建中通过 **window.yourLib.default** 访问，或在 CommonJS 构建中通过 **const myLib = require('mylib').default** 访问
  - 如果你没有任何具名导出并希望直接暴露默认导出，你可以在 vue.config.js 中使用以下 webpack 配置

  ```js
  module.exports = {
    configureWebpack: {
      output: {
        libraryExport: 'default'
      }
    }
  }
  ```

### Web Components 模式（不支持 IE11 及更低版本）

- 注意对 Vue 的依赖
  - 在 Web Components 模式中，Vue 是外置的。这意味着包中不会有 Vue，即便你在代码中导入了 Vue
  - 这里的包会假设在页面中已经有一个可用的全局变量 Vue
- 将一个单独的入口构建为一个 Web Components 组件
  - Vue CLI 将会把这个组件自动包裹并注册为 Web Components 组件，无需在 main.js 里自行注册。也可以在开发时把 main.js 作为 demo app 单独使用

```shell
# 注意这里的入口应该是一个 *.vue 文件
vue-cli-service build --target wc --name my-element [entry]
```

- 该构建将会产生一个单独的 JavaScript 文件 (及其压缩后的版本) 将所有的东西都内联起来
  - 当这个脚本被引入网页时，会注册自定义组件 `<my-element>`，其使用 **@vue/web-component-wrapper** 包裹了目标的 Vue 组件。这个包裹器会自动代理属性、特性、事件和插槽
  - 注意这个包依赖了在页面上全局可用的 Vue
  - 这个模式允许你的组件的使用者以一个普通 DOM 元素的方式使用这个 Vue 组件

```html
<script src="https://unpkg.com/vue"></script>
<script src="path/to/my-element.js"></script>

<!-- 可在普通 HTML 中或者其它任何框架中使用 -->
<my-element></my-element>
```

#### 注册多个 wc 组件的包

- 可以使用一个 glob 表达式作为入口指定多个组件目标： vue-cli-service build --target wc --name foo 'src/components/*.vue'
  - 当你构建多个 web component 时，--name 将会用于设置前缀，同时自定义元素的名称会由组件的文件名推导得出
  - 例如： 一个名为 HelloWorld.vue 的组件携带 --name foo 将会生成的自定义元素名为 `<foo-hello-world>`

#### 异步 wc 组件

- 当指定多个 Web Components 组件作为目标时，这个包可能会变得非常大，并且用户可能只想使用你的包中注册的一部分组件
- 异步 Web Components 模式会生成一个 code-split 的包，带一个只提供所有组件共享的运行时，并预先注册所有的自定义组件小入口文件
- 一个组件真正的实现只会在页面中用到自定义元素相应的一个实例时按需获取

```shell
vue-cli-service build --target wc-async --name foo 'src/components/*.vue'
```

```html
<script src="https://unpkg.com/vue"></script>
<script src="path/to/foo.min.js"></script>

<!-- foo-one 的实现的 chunk 会在用到的时候自动获取 -->
<foo-one></foo-one>
```

## TypeScript

### 路由中 import xxx 提示模块不存在

- 一般这种情况是没有export类出来，导出一个空类即可
  - 使用TS后，不允许空script存在，即使页面没有任何内容都需要加一个空类

### 给Vue设置全局变量后TS无法识别

- 需要在main.ts中加入声明

```ts
declare module 'vue/types/vue' {
  interface Vue {
    $ipc: {
      on(channel: string, listener: Function):never
      send(channel:string,data:any):never
    }
  }
}
```

### 无法识别this.$refs.xx

- 在对应类中添加注释即可

```ts
// 以element-ui的某个组件为例
// 可以直接从element-ui中引用声明文件，但是声明文件和实际的js有差异，不要盲信声明文件
// 默认情况下类中变量都必须要初始化，否则会报错。而我们只是需要声明变量而已，为此而关闭强制初始化配置或者手动初始化都不是最佳选择
// $refs!表名此变量不会是undefined，可以移除错误提示
import { ElTree } from 'element-ui/types/tree'
@Component
export default class Home extends Vue {
  $refs!:{
    tree:ElTree
  }
}
this.$refs.tree
```

- 类前面必须要有@Component，否则TS无法正确处理，写在类里面的逻辑基本无效

## 部署
