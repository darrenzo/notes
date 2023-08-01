# FAQs

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
- electron-store 的 migrations 设置，key指的是软件的版本号，只有在启动客户端时发现客户端版本号和本地配置文件记录的版本不一致时且才会去触发migrations中对应当前版本号的回调方法。如果是首次安装客户端，会先触发migrations中设置的比当前版本低的版本设置，然后根据编写的先后顺序（非版本号顺序）依次触发
  - 只要设置了migrations，就会在本地配置文件中添加
  
  ```json
    "__internal__": {
      "migrations": {
        "version": "8.10.5"
      }
    }
  ```

- electron-updater 在查询yml时，yml资源的url会默认带上 ?noCache=${hash:5} 查询字段，导致原url的cdn效果失效
  - 可以通过设置 autoUpdater.requestHeaders = { authorization: '' } 方式去除 ?noCache=${hash:5} 字段

- 即时查看electron-log的方法
  - windows: 把exe运行文件拖到bash运行
  - macos需要右键启动软件文件，选择“显示内容”，双击macos文件夹里的bin文件即可
