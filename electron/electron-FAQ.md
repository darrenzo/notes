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

- 加快构建非上线版本速度的配置
  - 关闭代码签名
  ```js
    // window
    delete process.env.CSC_LINK;
    delete process.env.CSC_KEY_PASSWORD;

    // mac  electron-builder
    mac.identity = null
    // 如果有公证流程，可以关闭公证notarize
  ```

  - 压缩等级
  ```js
    // electron-builder
    compression: IS_PROD ? 'normal' : 'store'
  ```

  - mac构建只需要dmg
  ```js
    target: ['dmg']
  ```

  - 不生成blockMap
  ```js
    // windows
    nsis.differentialPackage = false

    // mac
    dmg.writeUpdateInfo = false
  ```

  - mac添加公证的配置

```js
afterSign: async (context) => {

    const {
        electronPlatformName,
        appOutDir
    } = context;

    if (!IS_PROD || electronPlatformName !== 'darwin') {
        return;
    }

    const appName = context.packager.appInfo.productFilename;

    return await notarize({
        appBundleId: process.env.VUE_APP_APP_ID,
        appPath: `${appOutDir}/${appName}.app`,
        appleId: 'xxx',
        appleIdPassword: 'xxx'
    });

},

mac: {
    ...{
        icon: process.env.VUE_APP_PLATFORM_ICON,
        entitlements: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mac.plist'),
        entitlementsInherit: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mac.plist'), // 授予Electron在内部访问权限文件时相同的权限
        identity: IS_PROD ? process.env.VUE_APP_IDENTITY_NAME : null
    },
    ...(IS_PROD ? {
        hardenedRuntime: true, // 在添加公证时，苹果偷偷加入了一项要求，即App的 runtime 必须是 hardened runtime ，这在默认情况下会减少应用程序的权限
        gatekeeperAssess: false // MacOS 10.14.5 后进行完整性检查以验证签名是否成功会返回false,因为虽然签名进行完毕了，但应用程序还没有公证信息，所以会返回错False
    } : {
        target: ['dmg']
    })
},
```

## ffi-napi 在 arm64 电脑上安装的问题

- 4.0.1+ 才能在arm64 mac电脑上顺利安装，否则会出现ffi.h或 ffiConfig.h找不到的报错

## mac上架app store

- [electron上架说明](https://www.electronjs.org/zh/docs/latest/tutorial/mac-app-store-submission-guide)
- [electron-builder配置说明](https://www.electron.build/configuration/mac.html)
- [apple 账号 登录地址](https://developer.apple.com/account)
- 在本机生成 证书请求文件:  打开钥匙串 -> 点击左上角钥匙串访问 -> 证书助理 -> 从证书颁发机构申请 -> 填写 邮箱和名字（不会真正发邮件）-> 存储到磁盘
- 创建软件的appIds, 在apple账户中的 Identifiers 项创建
- 需要的keychain文件，在apple 账户中 Certificates项 生成，过程中需要选择上述的证书请求文件才能成功生成
  - Apple Root CA.cer (苹果根证书)
  - Apple Worldwide Developer Relations Certification Authority.cer (XCODE生成)
  - developerID_application.cer
  - distribution.cer
  - mac_installer.cer
  - xxx.provisionprofile (profile项中生成)
  - development.cer (需要在本地验证mas包时才需要) 
- 下载和安装生成的 cer 证书。如果是在本机安装的，自动会带上密钥，钥匙串中对应证书可以下拉显示密钥信息。如果要导出给别的电脑使用，需要导出 P12 和 cer 两份文件。
  - P12文件包含 cer 文件和密钥文件，有些mac电脑使用P12打开后没有cer文件，所以需要额外导出一份cer文件
- electron-builder配置

```js
//  ***** plist 文件尽量由mac电脑生成，否则windows生成的plist文件部分情况下不能被mac电脑成功解析
mac: {
      icon: process.env.VUE_APP_PLATFORM_ICON,
      entitlements: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mac.plist'),
      entitlementsInherit: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mac.plist'), // 授予Electron在内部访问权限文件时相同的权限
      identity: IS_PROD ? process.env.VUE_APP_IDENTITY_NAME : null
      hardenedRuntime: true, // 在添加公证时，苹果偷偷加入了一项要求，即App的 runtime 必须是 hardened runtime ，这在默认情况下会减少应用程序的权限
      gatekeeperAssess: false // MacOS 10.14.5 后进行完整性检查以验证签名是否成功会返回false,因为虽然签名进行完毕了，但应用程序还没有公证信息，所以会返回错False
      target: ['mas'] // 需要上架app store时开启
},
mas: {
  hardenedRuntime: false,
  provisioningProfile: path.join(process.env.VUE_APP_DIR_PLATFORM, 'XXX.provisionprofile'),
  entitlements: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mas.plist'),
  entitlementsInherit: path.join(process.env.VUE_APP_DIR_PLATFORM, 'entitlements.mas.inherit.plist'),
  gatekeeperAssess: false,
  asarUnpack: []
}

// plist 用来配置权限，对应apple账号中的 证书里的 identifiers =》 Capabilities, sandbox能力必须要

<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>com.apple.security.app-sandbox</key>
        <true/>
    </dict>
</plist>
```

- 构建成功后，把pkg文件 放入到 transport软件中进行上传并交付，交付成功后可以在[app store connect](https://appstoreconnect.apple.com/apps) 中找到自己的app信息
- 配置对应的信息，配置好后，可以在TestFlight项目中进行设置，macos上下载testFlight软件，进行测试