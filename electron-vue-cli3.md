# electron-vue-cli3

- 使用 [Vue CLI Plugin Electron Builder](https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/#installation) 进行构建
- 本文档基于 **vue-cli3** 和 **vue-cli-plugin-electron-builder v1.0.0**

## 安装

- 使用vue-cli3创建一个项目
- **vue add electron-builder** 安装插件
- **npm run electron:serve** 或者 **yarn electron:serve** 开启开发服务器
- **npm run electron:build** 或者 **yarn electron:build** 进行生产构建

## 本地模块

- 本地模块是自动支持的，且不需要任何配置即可运行
- 如果报错，首先需要确认 vue-cli-plugin-electron-builder 的版本是 1.0.0-rc.1 及以上
- 如果依旧报错，则需要使用 **vue invoke electron-builder** 再次调用生成器，生成器会自动检测缺失的代码（如本地模块支持）并添加，不会干扰其他代码
- 如果以上都试过之后，需要将本地依赖项设置为webpack外部依赖项

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      // List native deps here if they don't work
      externals: ['my-native-dep'],
      // If you are using Yarn Workspaces, you may have multiple node_modules folders
      // List them all here so that VCP Electron Builder can find them
      nodeModulesPath: ['../../node_modules', './node_modules']
    }
  }
}
```

- 如果在代码中不使用本地依赖项，可以从package.json中删除 **postinstall** script
  - 本地模块可能无法工作，但依赖项安装时间会更快

## 渲染进程

- **__static** 全局变量：在生产和开发环境下，它都提供了到公共目录的路径, 使用它来读取/写入应用程序的公共目录中的文件
  - 常规构建和服务中，该变量不可用，它应该只在 electron 中用于读取/写入磁盘上的文件
  - import文件时，为了不让webpack编译，可以使用 **process.env.BASE_URL** (绝对路径webpack不会编译)
- 在生产环境，**process.env.BASE_URL** 被替换为应用程序文件的路径

## 主线程(background.js)

- 主进程无法访问**process.env.BASE_URL**或**src/assets**。但是，仍然可以在开发和生产中使用**__static**来获取到您的公共目录的路径

```js
// Both renderer and main process
// This will read the contents of public/myText.txt
// Expects myText.txt to be placed in public folder
const fileLocation = path.join(__static, 'myText.txt')
```

## 生产构建文件结构

```shell
├── dist_electron/
│ ├── bundled/..  # where webpack outputs compiled files
│ ├── [target platform]-unpacked/..  # unpacked Electron app (main app and supporting files)
│ ├── [application name] setup [version].[target binary (exe|dmg|rpm...)]  # installer for Electron app
│ ├── index.js  # compiled background file used for electron:serve
│ └── ...
├── public/  # Files placed here will be available through __static or process.env.BASE_URL
├── src/
│ ├── background.[js|ts]  # electron entry file (for Electron's main process)
│ ├── [main|index].[js|ts]  # your app's entry file (for Electron's render process)
│ └── ...
├── package.json  # your app's package.json file
├── ...
```

## 环境变量

- 所有以 **VUE_APP_** 前缀的env变量都将在主进程和呈现程序进程中可用(从1.0.0-rc.4开始仅在主进程中可用)

## Build 命令工作原理

- 包括三大阶段：render build，main build，electron-builder build
  - Render build：调用一些自定义的配置运行 **vue-cli-service build** 以便可以正确地和 electron 进行工作（这个渲染进程就是指应用程序）
  - Main build：在这个阶段 vue-cli-plugin-electron-builder 为主进程打包 **src/background.js** 背景文件
  - Electron-builder build：这个阶段通过 **electron-builder** 把 web app 代码转成由 electron 运行的桌面应用程序代码

## Serve 命令工作原理

- 包括三大阶段：main build, dev server launch, and electron launch
  - Dev server launch：此阶段将启动内建的dev服务器，对其进行一些修改，以便正确地使用electronic
  - Main build：与build类似，打包了应用程序的主进程，但是处于开发环境
  - Electron launch：此阶段启动 electron 并让它加载以上开发服务器的URL

## 配置 Electron Builder

- [Electron Builder Configuration Options](https://www.electron.build/configuration/configuration)
  - 所有传递给electron:build的CLI参数都将被转发给electron-builder

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      builderOptions: {
        // options placed here will be merged with default configuration and passed to electron-builder
      }
    }
  }
}
```

## webpack 配置

```js
// vue.config.js
module.exports = {
  configureWebpack: {
    // 应用于所有构建的配置
  },
  pluginOptions: {
    electronBuilder: {
      chainWebpackMainProcess: config => {
        // 只对主进程生效
      },
      chainWebpackRendererProcess: config => {
        // 只对渲染进程生效
        // 例如设置 IS_ELECTRON 为 true
        config.plugin('define').tap(args => {
          args[0]['IS_ELECTRON'] = true
          return args
        })
      },
      // 设置主进程入口文件
      mainProcessFile: 'src/myBackgroundFile.js',
      // 设置一个文件数组，位于其中的文件如果被修改，则重编译主进程并重启electron
      // 主进程文件将被默认添加
      mainProcessWatch: ['src/myFile1', 'src/myFile2'],
      // [1.0.0-rc.4+] 提供了一份参数数组，electron将在 "electron:serve" 期间访问，可以在主进程（src/background.js）访问这些参数
      // 注意当 --debug 与 "electron:serve" 一起使用时，这些参数将被忽略，因为你必须自己启动 electron
      // 命令行参数（不包括--debug、--dashboard和--headless）也会传递给 electron
      mainProcessArgs: ['--arg-name', 'arg-value']
    }
  }
}
```

## 改变输出目录

- 为了不适用 "dist_electron" 默认输出目录，可以在 **vue-cli-plugin-electron-builder** 的插件选项中自定义
  - 如果使用的 **vue-cli-plugin-electron-builder** 是 **v1.0.0-rc.4** 及以上版本，则也可以用 **--dest** 参数修改输出目录

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      outputDir: 'electron-builder-output-dir'
    }
  }
}
```

- 如果使用的 **vue-cli-plugin-electron-builder** 是 **v1.0.0-rc.3** 及以下版本，更新完输出目录后必须更新 **package.json** 文件中的 **main** 字段为新的主程序文件（[new dir]/bundled/background.js），同时建议把新的输出目录添加进 **.gitignore** 文件

## TypeScript Options

- ts支持是自动的，不需要任何配置，只需要 "vue add @vue/typescript"

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      disableMainProcessTypescript: false, // 手动关闭在主进程使用的ts插件，之后可以在主进程（src/background.js）使用常规的js
      mainProcessTypeChecking: false // 手动关闭主进程的类型检查
    }
  }
}
```

- 在**1.0.0-rc.1+**,如果决定稍后再添加 "@vue/typescript" 插件，则要使用 **vue invoke electron-builder** 再次调用插件生成器，这会自动将缺失的类型定义插入到 **background.ts** 文件

## 改变文件加载协议

- 默认使用 vue-cli的现代模式下的 **type="module"** 方式去加载文件，如果想自定义不同的加载协议，需要使用 **customFileProtocol** 并修改 **background.js** 文件

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      customFileProtocol: 'myCustomProtocol://./' // 协议的末尾一定要加上 "./"
      customFileProtocol: 'file://./' // 也可以回到默认的文件协议 "file://"
    }
  }
}

// src/background.js
// ...
win.loadURL('myCustomProtocol://./index.html') // 同时要改变此处的协议
// ...
```

## 打包选项

- 默认的，应用程序使用现代模式构建，如果要禁止这种模式，可以在 "electron:build" 命令上传递 **--legacy** 这个参数
- 如果你的应用程序已经打包好了，且只需要使用 **electron-builder** 构建，那么可以传递 **--skipBundle** 这个参数

## electron 的终端上的垃圾输出

- electron 有时会生成一串垃圾输出（例如提示chrome的未捕获错误），**vue-cli-plugin-electron-builder** 默认关闭了这些输出
- 如果需要开启这些垃圾输出，那么需要设置 **electronJunkOutput** 属性

```js
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      removeElectronJunk: false // True by default
    }
  }
}
```

## 自定义启动程序和托盘的Icon

- 安装 **electron-icon-builder** 为开发依赖
  - yarn add --dev electron-icon-builder
  - npm install -D electron-icon-builder
- 设置Icon图片 **public/icon.png**
- **package.json** 添加生成script
  - "electron:generate-icons": "electron-icon-builder --input=./public/icon.png --output=build --flatten"
- 生成icon
  - yarn electron:generate-icons
  - npm run electron:generate-icon
- 以上只会生成各种大小的icon，一般不这样生成
- 设置托盘icon
  - 编辑 src/background.(js|ts)

  ```js
  'use-strict';

  import path from 'path'

  win = new BrowserWindow({
    width: 800,
    height: 600,
    // 添加这一行
    // 如果 __static 报错 not defined，则需要在imports代码上面添加 /* global __static */ 字段（试过发现好像没用，可以设置 declare const __static: any;）
    icon: path.join(__static, 'icon.png')
  })
  ```

## 为每个页面创建多个electron窗口

- 根据 [pages属性](https://cli.vuejs.org/config/#pages) 添加pages
- [案例](https://github.com/nklayman/electron-multipage-example)
- 为第二个页面创建变量
  - 在背景文件（默认是src/background.(js|ts)）中添加 **secondWin** 和 **createdAppProtocol** 变量

  ```js
  // Already in file
  let win
  // Add these below
  let secondWin
  let createdAppProtocol = false
  ```

- 在背景文件更新 createWindow 方法

```js
protocol.registerStandardSchemes(['reolink'], { secure: true })
// 给 createWindow 设置需要新增传入参数 winVar, devPath, prodPath
function createWindow(winVar, devPath, prodPath) {
  // Create the browser window.
  winVar = new BrowserWindow({ width: 800, height: 600 })

  if (process.env.WEBPACK_DEV_SERVER_URL) {
    // Load the url of the dev server if in development mode
    // loadURL 需要新增加上 devPath
    winVar.loadURL(process.env.WEBPACK_DEV_SERVER_URL + devPath)
    if (!process.env.IS_TEST) winVar.webContents.openDevTools()
  } else {
    if (!createdAppProtocol) {
      createProtocol('app')
      createdAppProtocol = true
    }
    // Load the index.html when not in development
    winVar.loadURL(`app://./${prodPath}`)
  }

  winVar.on('closed', () => {
    winVar = null
  })
}
```

- 在应用程序启动时创建两个窗口

```js
app.on('ready', async () => {
  if (isDevelopment && !process.env.IS_TEST) {
    // Install Vue Devtools
    try {
      await installVueDevtools()
    } catch (e) {
      console.error('Vue Devtools failed to install:', e.toString())
    }
  }
  // Replace
  createWindow()
  // With
  createWindow(win, '', 'index.html')
  createWindow(secondWin, 'subpage', 'subpage.html')
})
```

- 点击 Dock Icon 时再次创建两个窗口

```js
app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (win === null) {
    createWindow(win, '', 'index.html')
  }
  if (secondWin === null) {
    createWindow(secondWin, 'subpage', 'subpage.html')
}
```

## 使用 vscode 调试

- 开启 sourcemaps
- 在 **.vscode/tasks.json** 添加 electron-debug 任务，该任务会以 debug 模式开启 Electron dev server

```json
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
      "label": "electron-debug",
      "type": "process",
      "command": "./node_modules/.bin/vue-cli-service",
      "windows": {
        "command": "./node_modules/.bin/vue-cli-service.cmd"
      },
      "isBackground": true,
      "args": ["electron:serve", "--debug"],
      "problemMatcher": {
        "owner": "custom",
        "pattern": {
          "regexp": ""
        },
        "background": {
          "beginsPattern": "Starting development server\\.\\.\\.",
          "endsPattern": "Not launching electron as debug argument was passed\\."
        }
      }
    }
  ]
}
```

- 在 **.vscode/launch.json** 给各环境添加debug配置

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Electron: Main",
      "type": "node",
      "request": "launch",
      "protocol": "inspector",
      "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron",
      "windows": {
        "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron.cmd"
      },
      "preLaunchTask": "electron-debug",
      "args": ["--remote-debugging-port=9223", "./dist_electron"],
      "outFiles": ["${workspaceFolder}/dist_electron/**/*.js"]
    },
    {
      "name": "Electron: Renderer",
      "type": "chrome",
      "request": "attach",
      "port": 9223,
      "urlFilter": "http://localhost:*",
      "timeout": 30000,
      "webRoot": "${workspaceFolder}/src",
      "sourceMapPathOverrides": {
        "webpack:///./src/*": "${webRoot}/*"
      }
    }
  ],
  "compounds": [
    {
      "name": "Electron: All",
      "configurations": ["Electron: Main", "Electron: Renderer"]
    }
  ]
}
```

- 配置好后，即可在vue app 或者 background 文件上打上断点
- 在vscode中选择 **Electron: All** 配置运行
  - 在刚刚启动electron程序时，无法检测到断点，重新加载窗口可以生效

## 测试

- [Spectron](https://electronjs.org/spectron)
  - 当使用 Spectron 时，要确保设置了 process.env.IS_TEST 为 true，否则会加载dev tools 而 Spectron 会因此报错
- vue-cli-plugin-electron-builder 可以导出 testWithSpectron 方法，它会运行 **electron:serve**，但不会启动 electron，而是创建一个新的 Spectron app 附加到开发服务器
  - 也能用在 e2e 测试

  ```js
  // This example uses Jest, but any testing framework will work as well
  const { testWithSpectron } = require('vue-cli-plugin-electron-builder')

  test('a window is created', async () => {
    const { stdout, url, stopServe, app } = await testWithSpectron()
    // stdout electron:serve 的日志
    console.log(`electron:serve returned: ${stdout}`)
    // url 是由 electron:serve 创建的开发服务器的地址 is the url for the dev server created with electron:serve
    console.log(`the dev server url is: ${url}`)
    // app 是一个 spectron 实例，它被附加到开发服务器，然后被启动并等待加载
    expect(await app.client.getWindowCount()).toBe(1)
    // 在测试结束前，确保停止服务器和spectron
    await stopServe()
  })
  ```

  ```js
  // testWithSpectron 配置参数
  const { testWithSpectron } = require('vue-cli-plugin-electron-builder')

  testWithSpectron({
    noSpectron: false // 不启动Spectron. 如果想自己启动 spectron，就设置为true
    noStart: false // 不启动Spectron app 或等待它加载. 在运行任何测试之前，必须调用app.start()和app.client.waitUntilWindowLoaded()
    forceDev: false // 以开发 mode 运行 dev server. 默认以 production mode 运行
    mode: 'test', // 设置自定义 Vue env mode
    spectronOptions: {} // 设置自定义的配置传递给 Spectron. Spectron 自己已经有默认值
  })
  ```

## FAQs

### builds 时白屏，但在 serve 时正常工作

- 当 **vue-router** 在 history 模式下运行时，可能会导致此问题
  - 解决办法：在 Vue 组件根目录下添加 **mounted** 钩子
    - 这个方法不会影响 web builds

```js
// src/main.js
new Vue({
  router,
  render: h => h(App),
  mounted() {
    // Prevent blank screen in Electron builds
    this.$router.push('/')
  }
}).$mount('#app')
```

### **electron:serve** 卡在了 **Launching Electron...**

- 通常这个问题是在 **Vue Devtools** 无法安装时引起的，最有可能的是因为无法在你所在的地区访问Vue Devtools(例如中国)
  - 解决办法：在 **src/background.(js|ts)** 文件中删除 Vue Devtools 的安装步骤

  ```js
  if (isDevelopment && !process.env.IS_TEST) {
    // Install Vue Devtools
    await installVueDevtools()
  }
  ```

- 用electron来集成vue-devtools过于麻烦，一个简单的方法是使用远程调试
  - yarn global add @vue/devtools安装，然后运行vue-devtools，在模板index.html中引入脚本就可以开始调试了

### 异步函数中的异常没有记录到控制台

- 在 Vue App 入口文件 src/main.js 中添加以下代码

```js
process.on('unhandledRejection', error => {
  console.error(error)
})
```

### Electron 在 Node v11 上打开

- Electron 要使用 v2.0.14+ or v3.0.10+，Node使用 v11.2.0+

### 修改滚动条样式

```less
@gak-no-visible: rgba(0, 0, 0, 0);
html, body {
  overflow: hidden;
  height: 100%;
}
.gak-bg-no-visible {
  background-color: @gak-no-visible;
}
.gak-scroll {
  height: 100%;
  -webkit-overflow-scrolling: touch;
  overflow-y: auto;
  /*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
  &::-webkit-scrollbar {
    width: 8px;
    height: 4px;
    cursor: pointer;
    .gak-bg-no-visible;
  }
  /*定义滚动条轨道 内阴影+圆角*/
  &::-webkit-scrollbar-track {
    border: none;
    .gak-bg-no-visible;
  }
  /*定义滑块 内阴影+圆角*/
  &::-webkit-scrollbar-thumb{
    border-radius: 10px;
    background-color: rgba(110, 110, 110, 0.2);
  }
}
```

### 实时聊天组件

- 使用[vue-socket.io-extended](https://github.com/probil/vue-socket.io-extended)
  - Vue-Socket.io不再支持vuex
  - 采用[外部文件扩展的方式](https://github.com/probil/vue-socket.io-extended/issues/91#issuecomment-397232621),可以在action里调用

### 其他问题

- 大部分的问题都能通过重新调用 Vue CLI Plugin Electron Builder 生成器解决，这允许它更新代码到项目
  - 一般在升级插件后需要这样做

```shell
# In the root dir of your project
vue invoke electron-builder
```
