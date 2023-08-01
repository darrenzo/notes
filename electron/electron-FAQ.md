# FAQs

- electron版本有 4 升至 5以上时，会出现 nodeIntegration 相关报错
  - electron 5+版本创建窗口时， 默认把 webPreferences.nodeIntegration 设置为false, 需要手动在 options 里设置为true
- 解决 NODE_MODULE_VERSION 值不一致导致的运行失败问题 (项目根目录下运行)
  - `npm rebuild --runtime=electron --target=6.1.5 --disturl=https://atom.io/download/atom-shell --abi=73`
    - target 是指 electron的版本
    - abi 是指所要的NODE_MODULE_VERSION值
- app.isPackaged属性可以用来区分dev和build环境
- electron开机启动通过app.setLoginItemSettings({openAtLogin: open})设置，但在盗版系统下可能无效。在dev环境下开启时设置的启动项是项目node_modules/electron/dist/electron.exe文件，与build环境下的不同，是两个独立的启动项
- appId一样的软件，系统会识别为同一个
- package.json中的name值会应用为软件本地配置的文件夹名字
- 在创建BrowserWindow时传递选项中修改title值，可以设置软件打开后系统托盘显示的名字
- electron-log生成的log文件地址

  ```text
    on Linux: ~/.config/<app name>/log.log
    on macOS: ~/Library/Logs/<app name>/log.log
    on Windows: %USERPROFILE%\AppData\Roaming\<app name>\logs\log.log
  ```

- 崩溃报告在上传之前会临时存放在应用程序的用户数据目录下（Windows 和 MacOS 上文件夹名为 "Crashpad"，Linux 上是 "Crash Reports"）。 你可以在启动崩溃报告器之前，调用 app.setPath('crashDumps', '/path/to/crashes') 来覆盖该目录。
  - windows可以在 windows store 下载 WinDbg Preview 解析dump文件

```text
  on macOS: ~/Library/Application Support/<app name>/Crashpad/
  on Windows: %USERPROFILE%\AppData\Roaming\<app name>\Crashpad\reports\
```

- electron-updater 生成的yml文件中 stagingPercentage: 10 用来进行灰度发布，10代表有10%的用户会收到自动更新通知
- electron-store 的 migrations 设置，key指的是软件的版本号，只有在启动客户端时发现客户端版本号和本地配置文件记录的版本不一致时且才回去触发migrations中对应当前版本号的回调方法。如果是首次安装客户端，会先migrations中设置的比当前版本低的版本设置，然后根据编写的先后顺序（非版本号顺序）依次触发
  - 只要设置了migrations，就会在本地配置文件中添加
  
  ```json
    "__internal__": {
      "migrations": {
      "version": "8.10.5"
      }
    }
  ```
<!-- TODO 待续 -->

- electron-updater 在查询yml时，yml资源的url会默认带上 ?noCache=${hash:5} 查询字段，导致原url的cdn效果失效
  - 可以通过设置 autoUpdater.requestHeaders = { authorization: '' } 方式去除 ?noCache=${hash:5} 字段

- 即时查看electron-log的方法
  - windows: exe运行文件拖到bash运行
  - macos需要右键启动文件，选择显示内容，双击macos文件夹里的bin文件即可
