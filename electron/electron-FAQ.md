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
<!-- TODO 待续 -->
