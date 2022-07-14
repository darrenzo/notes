# Electron

- [Electron官网](https://electronjs.org/docs/tutorial/application-architecture)
- [VSCode使用教程](https://code.visualstudio.com/docs/)
- [Chromium的开发者工具集](https://developer.chrome.com/devtools)

## macos 环境配置步骤

1. nvm 配置，直接在github上nvm库的readme查看
2. npm上全局下载 node-gyp  yarn 等
3. npm config 配置

```shell
registry=https://registry.npmmirror.com
electron_mirror=https://cdn.npmmirror.com/binaries/electron/
electron_builder_binaries_mirror=https://npmmirror.com/mirrors/electron-builder-binaries/
```

4. 如果需要登录npm仓库账号，则进行登录
5. python下载：旧版mac系统自带python2,新版mac系统自带python3,使用which python查看
6. 下载xcode软件，下载后，打开命令行工具查看是否有异常打印，如果有，自行百度异常信息解决
7. 打开xcode软件，在左上角xcode项的菜单中找到preferences,打开并进入locations项，给Command Line Tools 设置 xcode版本
8. 下载并安装证书，一般双击 p12或cer文件进行安装。钥匙串中的证书显示不受信任的时候，双击证书，点击弹窗的信任一行，改为始终信任

## Electron应用结构

### 进程

- 主进程
  - Electron 运行 package.json 的 main 脚本的进程被称为主进程
  - 在主进程中运行的脚本通过创建web页面来展示用户界面
  - 一个 Electron 应用总是有且只有一个主进程
- 渲染进程
  - 由于 Electron 使用了 Chromium 来展示 web 页面，所以 Chromium 的多进程架构也被使用到
  - 每个 Electron 中的 web 页面运行在它自己的渲染进程中
- 在普通的浏览器中，web页面通常在一个沙盒环境中运行，不被允许去接触原生的资源。 然而 Electron 的用户在 Node.js 的 API 支持下可以在页面中和操作系统进行一些底层交互
- 主进程和渲染进程之间的区别
  - 主进程使用 BrowserWindow 实例创建页面
  - 主进程管理所有的web页面和它们对应的渲染进程
  - 每个 BrowserWindow 实例都在自己的渲染进程里运行页面
  - 当一个 BrowserWindow 实例被销毁后，相应的渲染进程也会被终止
  - 每个渲染进程都是独立的，它只关心它所运行的 web 页面
  - 在页面中调用与 GUI 相关的原生 API 是不被允许的，因为在 web 页面里操作原生的 GUI 资源是非常危险的，而且容易造成资源泄露。 如果你想在 web 页面里使用 GUI 操作，其对应的渲染进程必须与主进程进行通讯，请求主进程进行相关的 GUI 操作
- 进程间通讯
  - Electron为主进程（ main process）和渲染器进程（renderer processes）通信提供了多种实现方式
    - 可以使用[ipcRenderer](https://electronjs.org/docs/api/ipc-renderer)和[ipcMain](https://electronjs.org/docs/api/ipc-main)模块发送消息，使用[remote](https://electronjs.org/docs/api/remote)模块进行RPC方式通信
  - [web页面间如何共享数据](https://electronjs.org/docs/faq#how-to-share-data-between-web-pages)

### Electron的API简介

- Electron在主进程和渲染进程中提供了大量API去帮助开发桌面应用程序

```js
// 可以通过require的方式将其包含在模块中以此，获取Electron的API
const electron = require('electron');
```

- 所有Electron的API都被指派给一种进程类型。 许多API只能被用于主进程中，有些API又只能被用于渲染进程，又有一些主进程和渲染进程中都可以使用。(官方文档对每个API都标注了所适用的进程)

```js
// Electron中的窗口是使用BrowserWindow类型创建的一个实例， 它只能在主进程中使用
// 这样写在主进程会有用，但是在渲染进程中会提示'未定义'
const { BrowserWindow } = require('electron');

const win = new BrowserWindow();
```

- 因为进程之间的通信是被允许的, 所以渲染进程可以调用主进程来执行任务。 Electron通过remote模块暴露一些通常只能在主进程中获取到的API。 为了在渲染进程中创建一个BrowserWindow的实例，我们通常使用remote模块为中间件

```js
//这样写在渲染进程中时行得通的，但是在主进程中是'未定义'
const { remote } = require('electron')
const { BrowserWindow } = remote

const win = new BrowserWindow()
```

### Electron使用 Node.js 的 API

#### Electron同时在主进程和渲染进程中对Node.js 暴露了所有的接口

- 所有在Node.js可以使用的API，在Electron中同样可以使用

```js
const fs = require('fs')

const root = fs.readdirSync('/')
```

- 如果尝试加载远程内容， 这会带来重要的安全隐患。[安全指南文档](https://electronjs.org/docs/tutorial/security)
- 可以在应用程序中使用Node.js的模块
  - 原生Node.js模块 (即指，需要编译源码过后才能被使用的模块) 需要在编译后才能和Electron一起使用
  - [如何重新为Electron编译原生模块](https://electronjs.org/docs/tutorial/using-native-node-modules)

```js
// 准备好被使用的S3 client模块
const S3 = require('aws-sdk/clients/s3')
```

## 调试应用

### 渲染进程

- 可以通过编程的方式在`BrowserWindow`的`webContents中`调用`openDevTool()`API来打开它们

```js
const { BrowserWindow } = require('electron');

let win = new BrowserWindow();
win.webContents.openDevTools();
```

### 主进程

- [Chromium开发者工具可以被用来调试Electron的主进程](https://nodejs.org/en/docs/guides/debugging-getting-started/)

#### 命令行开关

- Electron浏览器窗口中的`DevTools`只能调试在该窗口中执行的`JavaScript` (即web页面)。为了提供一个可以调试主进程的方法，Electron提供了 `--inspect`和`--inspect-brk`开关

- `--inspect=[port]`: 当这个开关用于 Electron 时，它将会监听 V8 引擎中有关 port 的调试器协议信息。 默认的port 是 5858

```shell
electron --inspect=5858 your/app
```

- `--inspect-brk=[port]`: 和`--inspect`一样，但是会在JavaScript 脚本的第一行暂停运行

#### 外部调试器

- 需要使用一个支持 V8 调试协议的调试器
  - 通过访问`chrome://inspect`来连接`Chrome`, 并在那里选择需要检查的Electron应用程序
  - 使用`VSCode`进行主进程调试
    - 在 VSCode 中打开一个 Electron 项目。添加一个`.vscode/launch.json` 文件并使用以下配置：

    ```json
        {
          "version": "0.2.0",
          "configurations": [
            {
              "name": "Debug Main Process",
              "type": "node",
              "request": "launch",
              "cwd": "${workspaceRoot}",
              "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron",
              "windows": {
                "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron.cmd"
              },
              "args" : ["."],
              "outputCapture": "std"
            }
          ]
        }
    ```

    - 在 main.js中设置一些断点，并在 [Debug 视图](https://code.visualstudio.com/docs/editor/debugging) 中开始调试,捕获断点信息
    - [预先配置好的调试用例](https://github.com/octref/vscode-electron-debug/tree/master/electron-quick-start)

## 分发应用

- 为了使用 Electron 部署你的应用程序，你需要下载 Electron 的[prebuilt binaries](https://github.com/electron/electron/releases)
- 你存放应用程序的文件夹需要叫做`app`并且需要放在Electron的资源文件夹`Resources`下

```shell
# 在下面的示例中，Electron的预制二进制文件的位置用`electron/`表示

# 在 macOS 中:
electron/Electron.app/Contents/Resources/app/
├── package.json
├── main.js
└── index.html

# 在 Windows 和 Linux 中:
electron/resources/app
├── package.json
├── main.js
└── index.html
```

- 然后运行`Electron.app`(或者`Linux`中的`electron`，`Windows`中的 `electron.exe`), 接着 Electron 就会以你的应用程序的方式启动
- `electron`文件夹将被部署并可以分发给最终的使用者

### 打包应用程序

- 除了通过拷贝所有的资源文件来分发你的应用程序之外，你可以打包你的应用程序为一个[asar](https://github.com/electron/asar)档案文件
  - 缓解Windows下路径名过长的问题
  - 略微加快一下`require`的速度
  - 隐藏源代码
- 大部分用户可以毫不费力地使用这个功能，因为它[electron-packager](https://github.com/electron-userland/electron-packager),[electron-forge](https://github.com/electron-userland/electron-forge)和[electron-builder](https://github.com/electron-userland/electron-builder)中都得到了支持，开箱即用
- 为了使用一个`asar`档案文件代替`app`文件夹，你需要修改这个档案文件的名字为`app.asar`，然后将其放到Electron的资源文件夹下，然后Electron就会试图读取这个档案文件并从中启动

```shell
# 在 macOS 中:
electron/Electron.app/Contents/Resources/
└── app.asar

# 在 Windows 和 Linux 中:
electron/resources/
└── app.asar
```

- `asar`是一种将多个文件合并成一个文件的类 tar 风格的归档格式。 Electron 可以无需解压整个文件，即可从其中读取任意文件内容，详细步骤如下：
  1. 安装`asar`实用程序: `npm install -g asar`
  2. 使用`asar pack`打包: `asar pack your-app app.asar`
  3. 使用`asar`档案文件: 在Electron中有两类APIs：`Node.js` 提供的 `Node API` 和 `Chromium` 提供的 `Web API`。 这两种 API 都支持从`asar`档案中读取文件。
     1. Node API
        1. 由于 Electron 中打了特别补丁， Node API 中如 fs.readFile 或者 require 之类 的方法可以将 asar 视之为虚拟文件夹，读取 asar 里面的文件就和从真实的文件系统中读取一样

        ```shell
        # 假设我们在 /path/to 文件夹下有个 example.asar 包：
        $ asar list /path/to/example.asar
        /app.js
        /file.txt
        /dir/module.js
        /static/index.html
        /static/main.css
        /static/jquery.min.js
        ```

        ```js
        // 从 asar 档案读取一个文件：
        const fs = require('fs');
        fs.readFileSync('/path/to/example.asar/file.txt');

        // 列出档案根目录下的所有文件：
        const fs = require('fs');
        fs.readdirSync('/path/to/example.asar');

        // 使用档案中的模块：
        require('/path/to/example.asar/dir/module.js')

        // 使用 BrowserWindow 来显示一个 asar 档案里的 web 页面：
        const { BrowserWindow } = require('electron');
        const win = new BrowserWindow();

        win.loadURL('file:///path/to/example.asar/static/index.html');
        ```

     2. Web API
        1. 在 Web 页面里，用 `file:` 协议可以获取 asar 包中文件。和 Node API 一样，视 asar 包如虚拟文件

        ```html
        <!-- 用 $.get 获取文件: -->
        <script>
        let $ = require('./jquery.min.js')
        $.get('file:///path/to/example.asar/file.txt', (data) => {
          console.log(data)
        })
        </script>
        ```

- 把 asar 档案当作一个普通的文件: 某些情况下，如：对 asar 档案文件进行校验，我们需要像读取 “文件” 那样读取 asar 档案文件
  - 可以使用内置的没有asar功能的和原始fs模块一模一样的original-fs模块

  ```js
  const originalFs = require('original-fs');
  originalFs.readFileSync('/path/to/example.asar');
  ```

  - 可以将 process.noAsar 设置为 true 以禁用 fs 模块中对 asar 的支持
  
  ```js
  const fs = require('fs');
  process.noAsar = true;
  fs.readFileSync('/path/to/example.asar');
  ```

- Electron使得 asar 包在 Node API 下的应用尽可能的趋向于真实的目录结构，但仍有一些底层 Node API 无法被保证其正常工作
- 档案文件是只读的：档案文件中的内容不可更改，所以 Node APIs 里那些会修改文件的方法在使用 asar 归档文件时都无法正常工作
- 工作目录不能设置为档案文件里的目录：尽管 asar 档案是虚拟文件夹，但其实并没有真实的目录架构对应在文件系统里，所以你不可能将 working Directory 设置成 asar 包里的一个文件夹。 将 asar 中的文件夹以 `cwd` 形式作为参数传入一些 API 中也会报错
- 某些 API 需要额外解压档案包: 大部分 `fs` API 可以无需解压即从 asar 档案中读取文件或者文件的信息，但是在处理一些依赖真实文件路径的底层系统方法时，Electron 会将所需文件解压到临时目录下，然后将临时目录下的真实文件路径传给底层系统方法使其正常工作。 对于这类API，会增加一些开销。以下是一些需要额外解压的 API：
  - child_process.execFile
  - child_process.execFileSync
  - fs.open
  - fs.openSync
  - process.dlopen - 用在 require 原生模块时
- fs.stat 的不真实统计信息：对 asar 档案中的文件取 fs.stat，返回的 Stats 对象不是精确值，因为这些文件不是真实存在于文件系统里。 所以除了文件大小和文件类型以外，你不应该依赖 Stats 对象的值
- 执行asar档案内的二进制文件: Node 中有一些可以执行程序的 API，如 `child_process.exec`，`child_process.spawn` 和 `child_process.execFile` 等， 但只有 `execFile` 可以执行 asar 包中的程序。
  - 因为 `exec` 和 `spawn` 允许 `command` 替代 `file` 作为输入，而 command 是需要在 shell 下执行的. 目前没有 可靠的方法来判断 command 中是否在操作一个 asar 包中的文件，而且即便可以判断，依旧无法保证可以在无任何 副作用的情况下替换 command 中的文件路径
- 添加未打包的文件到 asar 档案: 一些 Node APIs 将在调用时将文件解包到文件系统。除了性能问题之外，这种行为还可能触发各种反病毒扫描器
  - 可以通过`--unpack`选项指定不压缩某个文件 `asar pack app app.asar --unpack *.node`
  - 运行该命令后，创建`app.asar`文件的同时还会创建一个名为`app.asar.unpacked`的文件夹，它包含未打包的文件，应该与`app.asar`归档文件一起发布

### 使用下载好的二进制文件进行重新定制

- 将应用程序捆绑到Electron后，可能需要在把应用分发给用户前将Electron进行重新定制

#### Windows

- 可以将 electron.exe 重命名为任何喜欢的名字，然后可以使用像 [rcedit](https://github.com/atom/rcedit) 那样的工具编辑它的 icon 和其他信息。

#### Linux

- 可以将 electron 重命名为任意喜欢的名字

#### macOS

- 可以将 Electron.app 重命名为任意喜欢的名字，然后也需要将一些文件中的 `CFBundleDisplayName`， `CFBundleIdentifier` 以及 `CFBundleName` 字段一并修改掉, 这些文件如下：
  - Electron.app/Contents/Info.plist
  - Electron.app/Contents/Frameworks/Electron Helper.app/Contents/Info.plist
- 也可以重命名帮助程序以避免它在系统活动监视器中显示为`Electron Helper`， 但是请确保你已经修改了帮助应用的可执行文件的名字

```shell
# 一个重命名后的应用程序的结构可能是这样的
MyApp.app/Contents
├── Info.plist
├── MacOS/
│   └── MyApp
└── Frameworks/
    └── MyApp Helper.app
        ├── Info.plist
        └── MacOS/
            └── MyApp Helper
```

### 打包工具

- 除了手动打包应用程序，还可以选择使用第三方打包工具
  - [electron-forge](https://github.com/electron-userland/electron-forge)
  - [electron-builder](https://github.com/electron-userland/electron-builder)
  - [electron-packager](https://github.com/electron-userland/electron-packager)

### 通过重编译源代码来进行重新定制

- 你也可以通过改变产品名称后从源码构建来重塑Electron的形象。 你只需要在 `args.gn` 文件中将构建参数设置为对应产品的名称(electron_product_name = "YourProductName")，并进行重新构建

### 创建一个自定义 Electron 分支

- 如果只是为了构建你的 app，你不需要创建一个自定义的 Electron 分支， 即使是“生产级”的应用程序。 可以使用工具，如 electron-packager 或 electron-builder 来 “重新定制” 你的 Electron app
- 如果你有个人定制的C++代码必须打补丁到Electron，而这些代码不能提交给上游或者已被官方版本拒绝，你就需要fork 你自己的Electron
  - 通过 surf-build 创建一个自定义版本
  - 通过 npm 安装 [Surf](https://github.com/surf-build/surf): `npm install -g surf-build@latest`
  - 创建一个新的 S3 bucket 并按照以下结构创建文件夹
  
  ```shell
  - electron/
    - symbols/
    - dist/
  ```

  - 设置以下环境变量：
    - `ELECTRON_GITHUB_TOKEN` - 一个在 GitHub 创建版本的 token
    - `ELECTRON_S3_ACCESS_KEY`, `ELECTRON_S3_BUCKET`, `ELECTRON_S3_SECRET_KEY` - 你将要上传的 Node.js 的 headers 以及 symbols 的位置
    - `ELECTRON_RELEASE` - 设置为 true，上传部分将运行，不设置 和 surf-build 只是做 CI-type 的检查，它只在每次发起拉取请求时运行
    - `CI` - 设置为 true ，否则无效
    - `GITHUB_TOKEN` - 设置为与 `ELECTRON_GITHUB_TOKEN` 相同
    - `SURF_TEMP` - 在 Windows 下设置为 C:\Temp 来防止路径太长的问题
    - `TARGET_ARCH` - 设置为 ia32 或 x64
  - 在 script/upload.py，你 必须 为你的分支(MYORG/electron)设置 ELECTRON_REPO， 尤其如果你本身是一个 Electron 贡献者。
  - `surf-build -r https://github.com/MYORG/electron -s YOUR_COMMIT -n 'surf-PLATFORM-ARCH'`
  - 需要很长的时间来等待构建完成

## [使用自定义驱动程序进行自动化测试](https://electronjs.org/docs/tutorial/automated-testing-with-a-custom-driver)

## 模板和命令行界面

- [Awesome Electron列表](https://github.com/sindresorhus/awesome-electron#boilerplates)
- electron-forge
  - Electron Forge将多个现有的（ 且有稳定维护的 ）Electron构建工具整合为一个简单易用的工具包，所有人都可以用它来快速地搭建Electron开发环境
  - Forge 将一些流行框架整合为“开箱即用”的模板，比如：React、Vue、Angular等。 Forge 的一些核心模块来自于上层的Electron社区（比如electron-packager）
- electron-builder
  - Electron Builder 是一个完备的Electron应用打包和分发解决方案，它致力于软件开发的集成体验
  - electron-builder 会将Electron维护者使用的模块和功能(例如: auto-updater) 替换为自定义的
- electron-react-boilerplate
  - 如果你不希望任何工具，而想要简单地从一个模板开始构建，`CT Lin`的 `electron-react-boilerplate` 可能值得一看。 它在社区中很受欢迎，并在内部使用了 `electron-builder`

## [代码签名](https://electronjs.org/docs/tutorial/code-signing)

## [桌面环境集成](https://electronjs.org/docs/tutorial/desktop-environment-integration)

<!-- TODO 待续 -->
