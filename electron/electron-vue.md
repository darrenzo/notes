# electron-vue

- [官方文档](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/)
- 该样板文档基于 **vue-cli** 版本
  - 如果项目需要基于 **vue-cli 3** 版本，可查看 **electron-vue-cli3.md** 或 [Vue CLI Plugin Electron Builder 官方文档](https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/#installation)
- 该样板代码所包含内容：
  - 基本的项目结构与 单一的 package.json 设置
  - 使用 vue-cli 作为项目脚手架
  - 立即可用的 Vue 插件 ([axios](https://github.com/axios/axios), [vue-electron](https://github.com/SimulatedGREG/vue-electron), vue-router, vuex)*
  - 预装开发工具 [vue-devtools](https://github.com/vuejs/vue-devtools) 和 [devtron](https://github.com/electron/devtron)
  - 使用 [electron-packager](https://github.com/electron-userland/electron-packager) 或 [electron-builder](https://github.com/electron-userland/electron-builder) 轻松打包你的应用程序*
  - appveyor.yml 与 .travis.yml 配置用于 electron-builder 的自动部署*
  - 能够生成用于浏览器的网页输出
  - 便利的 NPM 脚本
  - 使用携带模块热更新 (Hot Module Replacement) 的 webpack 和 vue-loader
  - 在工作在 electron 的 main 主进程时重启进程
  - 支持使用 vue-loader 的 HTML/CSS/JS 预处理器
  - 默认支持 [stage-0](https://babeljs.io/docs/plugins/preset-stage-0/) 的 ES6
  - 使用 [babili](https://github.com/babel/babili) 避免完全反编译到 ES5
  - ESLint (支持 standard 和 airbnb-base)*
  - 单元测试 (使用 Karma + Mocha)*
  - 端到端测试 (使用 Spectron + Mocha)*
- 基于 **electron-vue** 的作品：
  - [Surfbird](https://github.com/surfbirdapp/surfbird): 一个基于 Electron 和 Vue 的 Twitter 客户端
  - [Lulumi-browser](https://github.com/qazbnm456/lulumi-browser): Lulumi-browser 是一个轻量级的浏览器，基于 Vue.js 2 和 Electron
  - [Space-Snake](https://github.com/ilyagru/Space-Snake): 使用 Electron 和 Vue.js 构建的桌面游戏。
  - [Forrest](https://github.com/stefanjudis/forrest): 一个 npm 脚本的桌面客户端
  - [miikun](https://github.com/hiro0218/miikun): 一个简单的 Markdown 编辑器
  - [Dakika](https://github.com/Madawar/Dakika): 超省时的应用程序，使写作变得轻而易举
  - [Dynamoc](https://github.com/ieiayaobb/dynamoc): Dynamoc 是一个 dynamodb-local、 dynalite 和 AWS dynamodb 的图形化界面客户端
  - [Dockeron](https://github.com/dockeron/dockeron): Dockeron 项目, 基于 Electron + Vue.js 的 Docker 桌面客户端
  - [Easysubs](https://github.com/matiastucci/easysubs): 快速简单地下载字幕
  - [Data-curator](https://github.com/ODIQueensland/data-curator)：分享可用的开放数据
- 该样板代码被构建为 vue-cli 的一个模板，并且包含多个选项，可以自定义你最终的脚手架程序
- 本项目需要使用 node@^7 或更高版本
- 建议在创建项目之后锁定你的 electron 版本
  - 可以防止在同一项目上工作的其他开发者使用不同的版本进行开发
  - **electron** 经常发布新版本，所以一些功能特性总是会发生相应的变化
- electron-vue 官方推荐 **yarn** 作为软件包管理器，因为它可以更好地处理依赖关系，并可以使用 **yarn clean** 帮助减少最后构建文件的大小

## 安装

```shell
# 安装 vue-cli 和 脚手架样板代码
npm install -g vue-cli
vue init simulatedgreg/electron-vue my-project

# 安装依赖并运行你的程序
cd my-project
yarn # 或者 npm install
yarn run dev # 或者 npm run dev
```

- windows 用户注意事项
  - 如果在 npm install 期间遇到关于 node-gyp 的错误，那么你很有可能没有在你的系统上安装正确的构建工具（构建工具包括 Python 和 Visual Studio 等等）
  - 检查 npm 的版本，并确保它是最新的(使用yarn的可以跳过此项检查)
    - 可以使用 [npm-windows-upgrade](https://github.com/felixrieseberg/npm-windows-upgrade) 来完成
  - 设置所需的构建工具
    - 使用 [windows-build-tools](https://github.com/felixrieseberg/windows-build-tools) 来完成大部分工作
      - 全局安装此工具将依次设置 Visual C++ 软件包、Python 等等
  - 如果以上两步完成后，达不到所有工具都已成功安装，则需要安装一个干净的 **Visual Studio**

## 项目结构

- **electron-packager** 和 **electron-builder** 现在完全支持单一的 package.json 设置
- 我们需要确保我们本地的 npm 模块是针对 electron 来构建的
  - 可以使用 [electron-rebuild](https://github.com/electron/electron-rebuild)
  - 强烈建议使用 [electron-builder](https://github.com/electron-userland/electron-builder) 作为你的构建工具, 会更简单
- **src/main/index.dev.js** 文件专门用于开发以及安装开发工具，原则上，该文件不应该被修改，但是可以被用来扩展你的开发需求
  - 在构建的过程中，webpack 将介入其中并创建一个的捆绑，以 **src/main/index.js** 作为该捆绑的入口文件
- 文件树（某些文件或文件夹可能会根据在 vue-cli 脚手架中所选设置的不同而有所不同）

```shell
my-project
├─ .electron-vue
│  └─ <build/development>.js files
├─ build
│  └─ icons/
├─ dist
│  ├─ electron/
│  └─ web/
├─ node_modules/
├─ src
│  ├─ main
│  │  ├─ index.dev.js
│  │  └─ index.js
│  ├─ renderer
│  │  ├─ components/
│  │  ├─ router/
│  │  ├─ store/
│  │  ├─ App.vue
│  │  └─ main.js
│  └─ index.ejs
├─ static/
├─ test
│  ├─ e2e
│  │  ├─ specs/
│  │  ├─ index.js
│  │  └─ utils.js
│  ├─ unit
│  │  ├─ specs/
│  │  ├─ index.js
│  │  └─ karma.config.js
│  └─ .eslintrc
├─ .babelrc
├─ .eslintignore
├─ .eslintrc.js
├─ .gitignore
├─ package.json
└─ README.md
```

- 生产构建
  - 几乎所有的东西都在最终的生产构建中被删除

```shell
app.asar
├─ dist
│  └─ electron
│     ├─ static/
│     ├─ index.html
│     ├─ main.js
│     └─ renderer.js
├─ node_modules/
└─ package.json
```

## vue 组件

- 组件存放在 **src/renderer/components** 里
- 创建子组件时，一个常用的组织化实践是将它们放置在一个使用其父组件名称的新文件夹中（在协调不同的路由时，这一点特别有用）
  
```shell
src/renderer/components
├─ ParentComponentA
│  ├─ ChildComponentA.vue
│  └─ ChildComponentB.vue
└─ ParentComponentA.vue
```

## vue 路由

- 尽可能是用 vue 路由，而非 **BrowserWindows**
- 在使用 vue-router 时，不要使用 HTML5 历史模式
  - 此模式严格用于通过 http 协议提供文件，并且不能正常使用 file 协议，但是 electron 在生产构建中使用此协议提供文件

## 主进程

- main 进程本质上是一个完整的 node 环境，所以除了以下两个文件之外，并没有什么初始的项目结构
  - **src/main/index.js**
    - 是应用程序的主文件，electron 也从这里启动
    - 被用作 webpack 生产构建的入口文件
    - 所有的 main 进程工作都应该从这里开始
  - **app/src/main/index.dev.js**
    - 专门用于开发阶段
    - 会安装 **electron-debug** 和 **vue-devtools**
    - 一般不需要修改此文件，但它可以用于扩展你开发的需求
- 由于 main 进程是使用 webpack 来绑定的，所以使用 **__dirname** 和 **__filename** 将不会在生产阶段给你提供一个预期的值
  - 在生产阶段，main.js 被放在了 dist/electron 文件夹里面。应根据此点相应地使用 **__dirname** 和 **__filename**
- 如果需要 static/ 资源目录的路径（生产阶段dist文件内的electron文件夹下有个static文件夹），要会使用 **__static** 变量

## webpack 配置

- **electron-vue** 包含三个单独的、位于 **.electron-vue/** 目录中的 webpack 配置文件（web、main、renderer）
  - web 是可选的
  - main 和 renderer 两者都使用 **babel-preset-env** 来针对 node@7 的功能特性、使用babili、并把所有的模块都视为 externals
- **.electron-vue/webpack.main.config.js**
  - 针对 electron 的 main 进程
- **.electron-vue/webpack.renderer.config.js**
  - 针对 electron 的 renderer 进程
  - 此配置用来处理你的 Vue 应用程序，因此它包含 vue-loader 和许多其他可在官方 [vuejs-templates/webpack](https://github.com/vuejs-templates/webpac) 样板中找到的配置
  - 设置externals 白名单（较少用到）
    - 在某些情况下，对于提供原始的 *.vue 组件的 Vue UI 库，他们需要被列入白名单，以至于 vue-loader 能够编译它们
    - 使用 webpack 的 alias 时
    - 例如：设置 vue 来导入完整的 编译+运行环境 的构建，需要设置 vue 到白名单
- **.electron-vue/webpack.web.config.js**
  - 针对为浏览器构建你的 renderer 进程的源代码
  - 如果你需要把代码发布到网上，此配置则是其强大的起步基础
  - electron-vue 不支持更多其他的 Web 输出。 与 Web 输出相关的 Issues 很可能会被推迟或关闭

## 入口 index.html

- 在生产阶段，electron-vue 使用 **html-webpack-plugin** 创建 index.html
  - 在开发过程中，你将在 src/ 目录中找到一个 index.ejs。在这里，你可以更改 HTML 入口文件
  - 这个插件会自动将生产阶段的资源（包括 renderer.js 和 styles.css）注入到最终压缩版的 index.html 中

```html
<!-- 开发阶段的 index.ejs -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title><%= htmlWebpackPlugin.options.title %></title>
    <%= ... %>
  </head>
  <body>
    <div id="app"></div>
    <!-- webpack 的构建会被自动注入 -->
  </body>
</html>
```

```html
<!-- 生产阶段的 index.html (非压缩版) -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>app</title>
    <link href="styles.css" rel="stylesheet">
  </head>
  <body>
    <div id="app"></div>
    <script type="text/javascript" src="renderer.js"></script>
  </body>
</html>
```

- 不推荐 CDNs 的使用
  - 虽然使用 CDN 提供的资源有益于你的应用程序的最终版本的大小，但这必须要求应用程序始终可以访问互联网，而 Electron 应用程序并不总是如此
    - 例如使用 UI 框架，一旦离线，则app迅速变得乱七八糟
  - 如果坚持使用，可以通过将标签添加到 src/index.ejs 文件中达到目的，只是当你的应用程序处于离线模式时，请确保设置适当的 UI/UX 流程

## vue 插件

- axios：基于 Promise，用于浏览器和 node.js 的 HTTP 客户端
  - 可以在 main 进程脚本中轻松导入 axios，或者在 renderer 进程中使用 this.$http 或 Vue.http
- vue-electron：将 electron API 附加到 Vue 对象的 vue 插件，使所有组件可以访问它们
  - 一个可以轻松通过 this.$electron 访问 electron API 的简单的 vue 插件，不再需要将 electron 导入到每一个组件中
- vue-router 和 vuex

## NPM 脚本

- npm run build
  - 构建打包生产环境代码
- npm run dev
  - 在开发环境中运行程序
- npm run lint
  - 静态分析所有在 src/ 和 test/ 下面的 JS 以及 Vue 组件文件
- npm run lint:fix
  - 静态分析所有在 src/ 和 test/ 下面的 JS 以及 Vue 组件文件并且尝试修复问题
- npm run pack
  - 同时运行 npm run pack:main 和 npm run pack:renderer
  - npm run build 将处理此步骤，所以很少用
- npm run pack:main
  - 运行 webpack 来打包 main 进程的源代码
- npm run pack:renderer
  - 运行 webpack 来打包 renderer 进程的源代码
- npm run unit
  - 运行使用了 Karma + Jasmine 的单元测试
- npm run e2e
  - 运行使用了 Spectron + Mocha 的端对端测试
- npm test
  - 运行 npm run unit 和 npm run e2e

## 静态资源

```html
<!-- webpack 并不会捆绑图像 unsplash.png，并且应用程序可以看到 static/imgs/unsplash.png 目录里的资源 -->
<template>
  <img v-bind:src="imageUrl">
</template>

<script>
  export default {
    data () {
      // 注意 路径的起始是 `static/`
      return { imageUrl: 'static/imgs/unsplash.png' }
    }
  }
</script>
```

```js
import path from 'path'
var line = path.join(__static, '/someFile.txt');
```

- 在生产阶段，默认情况下，所有文件都包含 asar，因为它是被极力推荐的
  - static/ 文件夹中的资源只能在 electron 内部访问，因为只有 electron 了解此行为
  - 如果你打算将文件分发给用户，例如在外部程序中打开文件
    - 方法一：需要将这些资源从应用程序复制到用户的文档空间或桌面中
      - 从那里，你可以使用 electron 的 API [shell.openItem()](https://electron.atom.io/docs/api/shell/#shellopenitemfullpath) 来打开这些资源
    - 方法二：配置 electron-packager 或 electron-builder，设置特定文件，用来 “解压缩” 在生产阶段的 asar 存档 （electron-vue 并没有计划支持这种方法）

## 读写本地文件

- 为了避免 Chromium 的限制以及对应用程序内部文件的改写，请确保使用 electron 的 API，特别是 [app.getPath(name)](https://electron.atom.io/docs/api/app/#appgetpathname) 函数
  - 这个帮助函数可以使你获得指向系统目录的文件路径，如用户的桌面、系统临时文件 等等

### 案例

- yarn add nedb # 或 npm install nedb --save
- 设置 NeDB 并将其指向我们的 **userData** 目录
  - 这个空间专门为我们的应用程序所保留，所以，我们可以确信，其他程序 或 与其他用户的交互不应该篡改这个文件空间
  
  ```js
  // src/renderer/datastore.js
  import Datastore from 'nedb'
  import path from 'path'
  import { remote } from 'electron'

  export default new Datastore({
    autoload: true,
    filename: path.join(remote.app.getPath('userData'), '/data.db')
  })
  ```

  - 可以将数据存储导入到 src/renderer/main.js 里，并将其附加到 Vue 的 原型 (prototype) 上。通过在所有组件文件中使用 this.$db，我们现在可以访问数据存储的 API

  ```js
  import db from './datastore'

  /* 其它代码 */

  Vue.prototype.$db = db
  ```

## 构建

- electron-packager：适合刚开始制作 electron 应用程序或只需要创建简单的可执行文件 的情况
  - 针对所有平台的构建 npm run build
    - 并非所有操作系统都可以为所有其他平台进行构建
  - 针对特定平台的构建 npm run build:darwin
    - 平台包括 darwin、 mas、 linux 和 win32
  - 清除 npm run build:clean
    - 从 build 删除所有的构建文件
  - 非 Windows 用户注意事项
    - 如果想在非 Windows 平台上为 Windows 构建时 使用自定义图标，你必须安装 [wine](https://www.winehq.org/)
  - 默认的构建配置
    - 可以在 **.electron-vue/build.config.js** 中使用基于 **electron-packager** 的 [各种选项](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md#options) 进行进一步定制
    - 可以设置 package.json 里的 productName 来设置构建后的程序名称

    ```js
    {
        // 针对 'x64' 架构
        arch: 'x64',

        // 使用 'electron/asar' 压缩应用
        asar: true,

        // 应用程序的目录
        dir: path.join(__dirname, '../'),

        // 设置 electron 程序的图标
        // 基于平台添加文件的扩展
        //
        // 如果针对 Linux 进行构建, 请阅读
        // https://github.com/electron-userland/electron-packager/blob/master/docs/api.md#icon
        icon: path.join(__dirname, '../build/icons/icon'),

        // 忽略可能造成最后程序很大的文件
        ignore: /(^\/(src|test|\.[a-z]+|README|yarn|static|dist\/web))|\.gitkeep/,

        // 把构建结果存储到 `builds`
        out: path.join(__dirname, '../build'),

        // 重写现有构建
        overwrite: true,

        // 指定平台的环境变量
        platform: process.env.BUILD_TARGET || 'all'
    }
    ```

- electron-builder：适合需要完整的安装程序、自动更新的支持、使用 Travis CI 和 AppVeyor 的 CI 构建、或本机 node 模块的自动重建 的情况
  - 构建：npm run build
  - 构建未打包的目录：npm run build:dir
    - 生成简单的可执行文件，没有完整的安装程序。用于快速测试
  - 默认的构建配置
    - 可以在 package.json 中使用基于 electron-builder 的 [各种选项](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md#options) 进行进一步定制

  ```js
  "build": {
    "productName": "ElectronVue",
    "appId": "org.simulatedgreg.electron-vue",
    "dmg": {
      "contents": [
        {
          "x": 410,
          "y": 150,
          "type": "link",
          "path": "/Applications"
        },
        {
          "x": 130,
          "y": 150,
          "type": "file"
        }
      ]
    },
    "directories": {
      "output": "build"
    },
    "files": [
      "dist/electron",
      "node_modules/",
      "package.json"
    ],
    "mac": {
      "icon": "build/icons/icon.icns"
    },
    "win": {
      "icon": "build/icons/icon.ico"
    },
    "linux": {
      "icon": "build/icons"
    }
  }
  ```

  - 使用 CI 的自动化部署
    - 当使用 electron-vue 的 **electron-builder** 配置时，本项目还提供了用于自动部署的 **appveyor.yml** 和 **.travis.yml**
    - 两个配置文件都被设置好了用于构建你的 electron 应用，并将生成的程序推送到 GitHub 的发布页面、Bintray 等
    - Travis CI 用于构建 linux 和 darwin (macOS)，而 AppVeyor 用于构建 win32。这两项服务都是免费的 OSS 项目
    - 设置 Travis CI 和 AppVeyor 的步骤
      - 在 [Travis CI](https://travis-ci.org/getting_started) 或 [AppVeyor](https://www.appveyor.com/) 上创建一个帐户
      - 链接到你 electron-vue 项目的 GitHub 仓库
      - 去自己的 [github 设置页](https://github.com/settings/tokens) 生成新令牌 (同样的令牌可用于 Travis CI 和 AppVeyor 二者)
        - 设置一个 令牌描述
        - 检查 public_repo 的范围
        - 点击 生成令牌
      - 复制你的新令牌并保存以备以后使用
      - 打开 Travis CI 或 AppVeyor 上的仓库设置选项，添加一个新的 环境变量
        - 将变量的名称设置为 GH_TOKEN
        - 将变量的值设置为刚刚创建的 GitHub 的访问令牌
        - 保存新变量并确保加密措施已启用
      - 配置完毕，Travis CI / AppVeyor 在默认情况下会监测到你的 master 分支的任何推送
      - 推送完毕后，Travis CI / AppVeyor 将克隆你的仓库到其服务器并开始构建过程
      - 最后阶段，electron-builder 可以看到环境变量 GH_TOKEN，并创建一个发布草稿，并将生成的程序上传到你公共的 GitHub 仓库上
      - 可以编辑发布草稿，然后发布出去
      - 发布了发行版后，通过更新你的 package.json，你可以确保将来的版本标上新版本号
  - 自动更新
    - 必须要有代码签名才能启用应用程序自动更新的接收选项
      - 可以根据 [这里](https://github.com/electron-userland/electron-builder/wiki/Code-Signing) 描述的 electron-builder 需求来添加更多的环境变量用于设置代码签名
      - 安装证书后，你可以安装 electron-updater 并注释掉 src/main/index.js 底部的代码以启用自动更新
    - 如果没有代码签名证书，那么你可以随时使用 GitHub API 来检查新的软件发布
      - 当检测到新版本时，你的应用程序会提供一条通知，把用户导向可以下载并安装新版本的下载页面
      - 由于 electron-builder 提供的安装程序，用户无需卸载当前版本，新的安装将替代旧的版本，同时仍然保留任何 Web 存储或 userData 文件
