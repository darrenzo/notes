# npm

- 查看 npm 全局安装过的包
  - npm list -g --depth=0
- 查看 yarn 全局安装过的包
  - yarn global list --depth=0
- 如果有些包我们只会使用一次，或者只想尝试以下，不想安装到全局，也不想作为当前项目的依赖可以使用 npx 的方式来执行（npx 是 npm 5.2+ 版本之后自带的工具）
  - 更方便的执行当前项目中的可执行工具
    - node ./node_modules/.bin/mocha 等价于 npx mocha
  - 也可直接执行那些不在当前项目，也没在全局安装过的 npm 工具包
    - npx create-react-app my-app
    - npx 执行顺序
      - 先查看当前项目有没 create-react-app
      - 如果当前项目找不到，会去全局查找 create-react-app
      - 如果全局还找不到，会帮我们临时从 npm 包仓库安装 create-react-app，不会污染到当前项目，也不会装到全局
  
## yarn

```shell

yarn init

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

```
