# npm相关

## nvm

- [参考地址](https://www.cnblogs.com/yulinlewis/p/10441180.html)
- [nvm下npm安装相关设置](https://www.cnblogs.com/ejll/p/11229557.html)
- 装32位的node和使用32位的node， 都得在版本后空格加 32
- 装 node 16.15.x以上版本时，nvm需要1.1.19版本以上
- 当nvm命令出现 exit错误时，可能是因为权限不够，需要打开管理员权限运行

## npm

- 查看 npm 全局安装过的包
  - npm list -g --depth=0
- 查看 yarn 全局安装过的包
  - yarn global list --depth=0
- 全局安装脚手架的包时，推荐用npm。 yarn安装全局包时不会去设置环境变量，有的脚手架的全局命令找不到
- 如果npm 安装全局包失败时，可以先清除缓存再进行尝试
  - npm cache clean --force
- 如果有些包我们只会使用一次，或者只想尝试一下，不想安装到全局，也不想作为当前项目的依赖可以使用 npx 的方式来执行（npx 是 npm 5.2+ 版本之后自带的工具）
  - 更方便的执行当前项目中的可执行工具
    - node ./node_modules/.bin/mocha 等价于 npx mocha
  - 也可直接执行那些不在当前项目，也没在全局安装过的 npm 工具包
    - npx create-react-app my-app
    - npx 执行顺序
      - 先查看当前项目有没 create-react-app
      - 如果当前项目找不到，会去全局查找 create-react-app
      - 如果全局还找不到，会帮我们临时从 npm 包仓库安装 create-react-app，不会污染到当前项目，也不会装到全局
- [windows-build-tools](https://github.com/felixrieseberg/windows-build-tools)
  - 高版本的node.js自带这类构建工具，不需要额外安装此工具
  - 需要使用VC环境编译原生node模块包时使用的工具（普通编译会出现node-gyp报错）
  - npm config list //查看基本配置
  - npm config list -l //查看所有配置
  - npm install --global windows-build-tools
    - 最好使用powershell 管理员模式
  - yarn global add windows-build-tools
- npm 设置淘宝镜像
  - npm get registry
  - npm config set registry https://registry.npmmirror.com/
  - npm config set electron_mirror https://cdn.npmmirror.com/binaries/electron/
  - npm config set electron_builder_binaries_mirror https://npmmirror.com/mirrors/electron-builder-binaries/
  - 还原
    - npm config set registry https://registry.npmjs.org/
  - 安装 cnpm
    - npm install -g cnpm --registry=https://registry.npm.taobao.org

## yarn

```shell

npm install -g yarn

## 注意，要检查系统环境变量中是否有yarn路径，如果没有，全局安装的包不会生效
yarn global bin ## 查看yarn的安装目录

## 查看镜像地址
yarn config get registry

## 设置淘宝镜像
yarn config set registry https://registry.npmmirror.com/
yarn config set electron_mirror https://cdn.npmmirror.com/binaries/electron/
yarn config set electron_builder_binaries_mirror https://npmmirror.com/mirrors/electron-builder-binaries/

npm init ## 需要用npm初始化得到package.json

yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]

yarn add [package] --dev  ## devDependencies
yarn add [package] --peer  ## peerDependencies
yarn add [package] --optional ## optionalDependencies

yarn remove [package]

## 安装全部依赖
yarn 或 yarn install

## 全局安装
yarn global add [package]

## 升级
yarn upgrade [package]@ver

## 升级到最新
yarn upgrade –-latest [package]

# 查看yarn缓存存放路径
yarn cache dir

# 清除yarn缓存
yarn cache clean

```

## cross-env 插件

- [github地址](https://github.com/kentcdodds/cross-env#readme)
- 能跨平台地设置及使用环境变量， 可以在package.json中自定义scripts命令

```json
// yarn dev luda   => $1 值为 "luda"  调取 .env.luda文件与 .env 文件的环境变量
// yarn dev luda 相当于  npm run dev -- luda
// 若 yarn dev aaa 时，没有.env.aaa 文件，则只取 .env文件的环境变量，不会报错
"scripts": {
  "lint": "vue-cli-service lint --no-fix",
  "build": "cross-env NODE_ENV=production VUE_APP_IS_TEST=true webpack --config build/webpack.config.js",
  "dev": "cross-env-shell \"vue-cli-service electron:serve --mode $1\"",
  "test": "cross-env-shell NODE_ENV=production VUE_APP_IS_TEST=true \"vue-cli-service electron:build --legacy --mode $1\"",
  "release": "cross-env-shell NODE_ENV=production \"vue-cli-service electron:build --legacy --mode $1\""
}
```

## serve 服务器

```shell

yarn global add serve
cd [用作服务器的文件夹路径]
serve .

```

## NPM包的发布到私有npm仓库

- npm set @reolink:registry "https://npm.reolink.dev"
- 项目 package.json

```json
{
  "name": "@reolink/cli.ref",
  "scripts": {
    "lint": "tslint src/**/*",
    "rebuild": "npm run clean && npm run lint && npm run build",
    "clean": "rm -rf ./bc-sdk",
    "prepublishOnly": "npm run rebuild",
    "build": "tsc -p tsconfig.json"
  }
}
// 开发环境 tsconfig.json 的 sourceMap 和 declarationMap 要设置true, 生产环境的设置false
// 执行 yarn rebuild 生成 运行时代码
// 执行 npm publish --dry-run 模仿 publish 过程但不会实际发布，查看会发布的东西是否正确
// 执行 npm login --registry 'https://npm.reolink.dev' --auth-type=legacy 填写登录信息登录（此步需要账户有登录的权限）
// 执行 npm publish --tag dev 发布dev 开发版本，项目中使用，可以 npm i @reolink/cli.ref@dev 使用开发版 (tag概念类似于分支，version不能与已有的重复)
// 执行 npm publish 发布正式版

// NPM v9.x 版本对登录&认证协议进行了改版，如果要登陆基于旧版认证协议的私有的 NPM 服务器，应当增加 --auth-type=legacy 参数。如
// npm login --auth-type=legacy --registry 'https://npm.reolink.dev'
```
