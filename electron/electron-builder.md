# electron-builder

- [Guides](https://www.electron.build/code-signing)

## build

### windows

- npm run build --ia32
- npm run build --x64
- 配置config同时build 32位和64位的软件

```js
"win": {
  "target": [
    {
      "target": "nsis",
      "arch": [
        "x64",
        "ia32"
      ]
    }
  ]
}
```

## 代码签名

- Windows是双代码签名的(SHA1和SHA256哈希算法)
- 在macOS开发机器上，将自动使用来自密钥链的有效且适当的标识

### env

- CSC_LINK
  - 指向证书地址的链接
  - 可以是：HTTPS link、base64编码数据、**file://** link、本地路径
  - 支持主目录的简写形式 `~/`
- CSC_KEY_PASSWORD
  - 用于解密CSC_LINK中给出的证书的密码
- CSC_NAME
  - 仅仅指macOS上的证书名称(从login.keychain检索)。如果有多个身份(否则不要指定它)，那么它在开发机器上很有用(不是在CI上)
- CSC_IDENTITY_AUTO_DISCOVERY
  - Boolean值，默认为**true**
  - 在macOS开发机器上，是否自动使用来自密钥链的有效且适当的标识
- CSC_KEYCHAIN
  - 钥匙链上的名称，默认为**系统默认密钥链**
  - 未指定CSC_LINK时则使用
- 如果正在macOS上构建Windows，并且需要设置不同的证书和密码(与CSC_* env vars中设置的证书和密码不同)，可以使用**WIN_CSC_LINK**和**WIN_CSC_KEY_PASSWORD**

### Windows上代码签名证书

- 向数字证书颁发机构购买证书，得到例如**aaa.pfx**文件和密码bbb
- 在用户配置文件~/.bash_profile设置环境变量

```bash
# 例如
export CSC_LINK=~/aaa.pfx
export CSC_KEY_PASSWORD=bbb
```

- 运行build，electron-builder插件会自动进行代码签名

## [自动更新](https://www.electron.build/auto-update)

- 自动更新官网页有github案例
- [网上mac案例](https://segmentfault.com/a/1190000012904543)

### [设置publish参数](https://www.electron.build/configuration/publish)

- 值可以是 `String | Object | Array<Object | String>`
  - Object 可以是 Bintray, Generic Server, GitHub, S3, Spaces 或 Snap Store 的可选选项集合
  - array值指同时发布到所设置的服务器，但是自动更新所使用的的服务器默认为第一个
  - 如果设置了**GH_TOKEN**环境变量，则默认为github, 值为github仓库指定为repo建的token
  - 如果设置了**BT_TOKEN**,并且没有设置**GH_TOKEN**，则默认为bintray
- publish属性可以设置在vue.config.js的pluginOptions.electronBuilder.builderOptions对象的直接子级，或者给每个系统（mac、win、linux）设置一个
- 使用github用作自动更新平台，可以参考[readme](https://github.com/web-pencil/electron-updater-example)

```js
// 自定义一个平台，手动/自己写脚本上传最新文件到目标服务器
"publish": [
    {
      "provider": "generic",
      "url": "https://bucket_name.s3.amazonaws.com/download/", // baseurl  这里文件夹只放安装包和publish生成的latest.yml文件即可，每次更新s3的两个文件（要同时更新），app开启会检测latest.yml文件，如果发生更新，则会自动进行更新下载, 只有版本号不一样才会认为是更新了
    }
  ]
```

- electron-builder 的 publish 命令
  - **--publish, -p [choices: "onTag", "onTagOrDraft", "always", "never"]**
    - github平台，选择always时，执行publish命令会构建打包后自动对远程仓库进行上传安装包和latest.yml等4个文件，并建立好release draft, 去github手动检查无误后再次点击publish即可发布release成功
