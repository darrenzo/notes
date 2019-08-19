# vue-loader

- 可以通过 vue-cli直接创建一个项目的脚手架
- 也可以手动设置，以下：

## 安装

- 应该将 vue-loader 和 vue-template-compiler 一起安装——除非你是使用自行 fork 版本的 Vue 模板编译器的高阶用户
  - vue-template-compiler 需要独立安装的原因是你可以单独指定其版本
  - 每个 vue 包的新版本发布时，一个相应版本的 vue-template-compiler 也会随之发布
  - 编译器的版本必须和基本的 vue 包保持同步，这样 vue-loader 就会生成兼容运行时的代码，每次升级项目中的 vue 包时，也应该匹配升级 vue-template-compiler

## webpack 配置

- 除了通过一条规则将 vue-loader 应用到所有扩展名为 .vue 的文件上之外，请确保在你的 webpack 配置中添加 Vue Loader 的插件（其他loader不用）

```js
// webpack.config.js
const VueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
  module: {
    rules: [
      // ... 其它规则
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  plugins: [
    // 请确保引入这个插件！
    // 将你定义过的其它规则复制并应用到 .vue 文件里相应语言的块
    // 例如，如果你有一条匹配 /\.js$/ 的规则，那么它会应用到 .vue 文件里的 <script> 块
    new VueLoaderPlugin()
  ]
}
```

### sass

- 需要使用缩进时，需要设置选项

```js
// webpack.config.js -> module.rules
{
  test: /\.sass$/,
  use: [
    'vue-style-loader',
    'css-loader',
    {
      loader: 'sass-loader',
      options: {
        indentedSyntax: true
      }
    }
  ]
}
```

### PostCSS

- **Vue Loader v15** 不再默认应用 PostCSS 变换。需要通过 postcss-loader 使用 PostCSS
  - npm install -D postcss-loader
  - PostCSS 的配置可以通过 postcss.config.js 或 postcss-loader 选项来完成

```js
// webpack.config.js -> module.rules
{
  test: /\.css$/,
  use: [
    'vue-style-loader',
    {
      loader: 'css-loader',
      options: { importLoaders: 1 }
    },
    'postcss-loader'
  ]
}
```

### Babel

- npm install -D babel-core babel-loader
  - Babel 的配置可以通过 .babelrc 或 babel-loader 选项来完成

```js
// webpack.config.js -> module.rules
{
  test: /\.js?$/,
  loader: 'babel-loader'
}
```

### 排除 node_modules

- **exclude: /node_modules/** 常应用于 .js 文件的 JS 转译规则
- 鉴于 v15 中的推导变化，如果你导入一个 node_modules 内的 Vue 单文件组件，它的 `<script>` 部分在转译时将会被排除在外
  - 为了确保 JS 的转译应用到 node_modules 的 Vue 单文件组件，你需要通过使用一个排除函数将它们加入白名单

```js
{
  test: /\.js$/,
  loader: 'babel-loader',
  exclude: file => (
    /node_modules/.test(file) &&
    !/\.vue\.js/.test(file)
  )
}
```

### TypeScript

- npm install -D typescript ts-loader

```js
// webpack.config.js
module.exports = {
  resolve: {
    // 将 `.ts` 添加为一个可解析的扩展名。
    extensions: ['.ts', '.js']
  },
  module: {
    rules: [
      // ... 忽略其它规则
      {
        test: /\.ts$/,
        loader: 'ts-loader',
        options: { appendTsSuffixTo: [/\.vue$/] }
      }
    ]
  },
  // ...plugin omitted
}
```

### Pug (模板预处理)

- 绝大对数 webpack 的模板类 loader，诸如 **pug-loader**，会返回一个模板函数而不是一个编译好的 HTML 字符串
- 我们需要使用一个返回原始的 HTML 字符串的 loader，例如 **pug-plain-loader**，而不是使用 **pug-loader**
- npm install -D pug pug-plain-loader

```js
// webpack.config.js -> module.rules
{
  test: /\.pug$/,
  loader: 'pug-plain-loader'
}
```

```html
<template lang="pug">
div
  h1 Hello world!
</template>
```

- 如果使用它在 JavaScript 中将 **.pug** 文件作为字符串导入，你需要在这个预处理 loader 之后链上 **raw-loader**
  - 注意添加 raw-loader 会破坏 Vue 组件内的用法，所以你需要定义两条规则，其中一条指向使用了一个 resourceQuery 的 Vue 文件，另一条指向 (回退到) JavaScript 导入

```js
// webpack.config.js -> module.rules
{
  test: /\.pug$/,
  oneOf: [
    // 这条规则应用到 Vue 组件内的 `<template lang="pug">`
    {
      resourceQuery: /^\?vue/,
      use: ['pug-plain-loader']
    },
    // 这条规则应用到 JavaScript 内的 pug 导入
    {
      use: ['raw-loader', 'pug-plain-loader']
    }
  ]
}
```

### CSS Modules

- 不推荐使用
- 详见 **vue-cli.md 中的 CSS Modules**

### 热重载

- 状态保留规则
  - 当编辑一个组件的 `<template>` 时，这个组件实例将就地重新渲染，并保留当前所有的私有状态。能够做到这一点是因为模板被编译成了新的无副作用的渲染函数
  - 当编辑一个组件的 `<script>` 时，这个组件实例将就地销毁并重新创建。(应用中其它组件的状态将会被保留) 是因为 `<script>` 可能包含带有副作用的生命周期钩子，所以将重新渲染替换为重新加载是必须的，这样做可以确保组件行为的一致性。这也意味着，如果你的组件带有全局副作用，则整个页面将会被重新加载
  - `<style>` 会通过 **vue-style-loader** 自行热重载，所以它不会影响应用的状态

### 用法

- 当使用脚手架工具 **vue-cli** 时，热重载是开箱即用的
- 当手动设置你的工程时，热重载会在你启动 **webpack-dev-server --hot** 服务时自动开启
- 高阶用法查阅 **vue-loader** 内部使用的 [vue-hot-reload-api](https://github.com/vuejs/vue-hot-reload-api)

### 关闭热重载

- 热重载默认是开启的，除非遇到以下情况
  - webpack 的 target 的值是 node (服务端渲染)
  - webpack 会压缩代码
  - process.env.NODE_ENV === 'production'
- 设置 **hotReload: false** 选项来显式地关闭热重载

```js
module: {
  rules: [
    {
      test: /\.vue$/,
      loader: 'vue-loader',
      options: {
        hotReload: false // 关闭热重载
      }
    }
  ]
}
```

### Css提取

- 只在生产环境下使用 CSS 提取，这将便于你在开发环境下进行热重载
- webpack 4+ 使用 **mini-css-extract-plugin**

```js
// webpack.config.js
var MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  // 其它选项...
  module: {
    rules: [
      // ... 忽略其它规则
      {
        test: /\.css$/,
        use: [
          process.env.NODE_ENV !== 'production'
            ? 'vue-style-loader'
            : MiniCssExtractPlugin.loader,
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    // ... 忽略 vue-loader 插件
    new MiniCssExtractPlugin({
      filename: 'style.css'
    })
  ]
}
```

- webpack 3 使用 **extract-text-webpack-plugin**

```js
// webpack.config.js
var ExtractTextPlugin = require("extract-text-webpack-plugin")

module.exports = {
  // 其它选项...
  module: {
    rules: [
      // ...其它规则忽略
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract({
          use: 'css-loader',
          fallback: 'vue-style-loader'
        })
      }
    ]
  },
  plugins: [
    // ...vue-loader 插件忽略
    new ExtractTextPlugin("style.css")
  ]
}
```

## 代码校验

### ESlint

- **eslint-plugin-vue** 同时支持在 Vue 单文件组件的模板和脚本部分的代码校验
  - 有 **.eslintrc.js** 文件后，从命令行运行 `eslint --ext js,vue MyComponent.vue`

```js
// .eslintrc.js
module.exports = {
  extends: [
    "plugin:vue/essential"
  ]
}
```

- 或者使用 **eslint-loader**，`*.vue` 文件在开发过程中每次保存的时候就会自动进行代码校验
  - npm install -D eslint eslint-loader

  ```js
  // webpack.config.js
  module.exports = {
    // ... 其它选项
    module: {
      rules: [
        {
          // 确保它是作为一个 pre-loader 运用的
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /node_modules/
        }
      ]
    }
  }
  ```

### stylelint

- 支持在 Vue 单文件组件的样式部分的代码校验
- 命令行式校验 `stylelint MyComponent.vue`
- 使用 **stylelint-webpack-plugin**
  - npm install -D stylelint-webpack-plugin

  ```js
  // webpack.config.js
  const StyleLintPlugin = require('stylelint-webpack-plugin');
  module.exports = {
    // ... 其它选项
    plugins: [
      new StyleLintPlugin({
        files: ['**/*.{vue,htm,html,css,sss,less,scss,sass}']
      })
    ]
  }
  ```