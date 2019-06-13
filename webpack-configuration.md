# webpack-configuration

- [英文网](https://webpack.js.org/configuration)
- [中文网](https://www.webpackjs.com/configuration/)

## 配置

- webpack 是需要传入一个配置对象。取决于你如何使用 webpack，可以通过两种方式之一：终端或 Node.js
- 整个配置中使用 Node 内置的 path 模块，并在它前面加上 __dirname这个全局变量。可以防止不同操作系统之间的文件路径问题，并且可以使相对路径按照预期工作
- 整个配置选项详见官网第一栏

## 使用不同语言进行配置

- webpack 接受以多种编程和数据语言编写的配置文件
- 支持的文件扩展名列表，可以在 [node-interpret](https://github.com/js-cli/js-interpret) 包中找到
- 官网有 ts、CoffeeScript、jsx的配置指南

## 多种配置类型

- 在开发和生产构建之间，消除 webpack.config.js 的差异的方法有多种

### 导出一个配置对象

### 导出为一个函数

- 环境对象（env）作为第一个参数
- 一个选项 map 对象（argv）作为第二个参数
  - 这个对象描述了传递给 webpack 的选项，并且具有 output-filename 和 optimize-minimize 等 key

```js
module.exports = function(env, argv) {
  return {
    mode: env.production ? 'production' : 'development',
    devtool: env.production ? 'source-maps' : 'eval',
    plugins: [
      new webpack.optimize.UglifyJsPlugin({
        compress: argv['optimize-minimize'] // 只有传入 -p 或 --optimize-minimize
      })
    ]
  };
};
```

### 导出一个 Promise

- webpack 将运行由配置文件导出的函数，并且等待 Promise 返回。便于需要异步地加载所需的配置变量

```js
module.exports = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({
        entry: './app.js',
        /* ... */
      })
    }, 5000)
  })
}
```

### 导出多个配置对象

- 当运行 webpack 时，所有的配置对象都会构建。例如，导出多个配置对象，对于针对多个构建目标（例如 AMD 和 CommonJS）打包一个 library 非常有用

```js
module.exports = [{
  output: {
    filename: './dist-amd.js',
    libraryTarget: 'amd'
  },
  entry: './app.js',
  mode: 'production',
}, {
  output: {
    filename: './dist-commonjs.js',
    libraryTarget: 'commonjs'
  },
  entry: './app.js',
  mode: 'production',
}]
```

## 上下文 context

- 基础目录，用于从配置中解析入口起点和 loader 的路径
- 绝对路径，默认是用当前目录，但是推荐在配置中传递一个值，这使得你的配置独立于 CWD(current working directory - 当前执行路径)

## externals

- externals配置就是为了使**import _ from 'lodash'**这句代码，在本身不引入lodash的情况下，能够在各个环境都能解释执行 （假设要引入lodash）
  - 如果你写的库要支持各种环境，你需要设置output中的libraryTarget为umd，也就是将打包的文件，生成为umd规范，适用于各种环境
  - externals的配置有以下几种：array , object ,reg。这三种形式都可以传入，前者其实是对后者的包含
    - 数组内的每一个元素又可以是多种形式，包括object, reg, function, string

```js
externals: {
  "lodash": {
        commonjs: "lodash",//如果我们的库运行在Node.js环境中，import _ from 'lodash'等价于const _ = require('lodash')
        commonjs2: "lodash",//同上
        amd: "lodash",//如果我们的库使用require.js等加载,等价于 define(["lodash"], factory);
        root: "_"//如果我们的库在浏览器中使用，需要提供一个全局的变量‘_’，等价于 var _ = (window._) or (_);
  }
}
```

- externals 支持以下模块上下文(module context)
  - global - 外部 library 能够作为全局变量使用。用户可以通过在 script 标签中引入来实现。这是 externals 的默认设置
  - commonjs - 用户(consumer)应用程序可能使用 CommonJS 模块系统，因此外部 library 应该使用 CommonJS 模块系统，并且应该是一个 CommonJS 模块
  - commonjs2 - 类似上面几行，但导出的是 module.exports.default
  - amd - 类似上面几行，但使用 AMD 模块系统
- 不同环境设置externals方式
  - 如果你的代码想运行在Node环境中，那么你需要在external中添加前缀commonjs2或者commonjs

  ```js
  externals:{
    react:'commonjs2 react',
    jquery:'commonjs2 jquery'
  }
  ```

  - 如果需要requirejs等符合AMD规范的环境中加载，那就要添加amd

  ```js
  externals:{
    react:'amd React',
    jquery:'amd jQuery'
  }
  ```

  - 如果要在浏览器中运行，那么不用添加什么前缀，默认设置就是global
    - 这种方式配置下，就是配置你所引用你的库暴露出的全局变量
    - 如果你想运行代码在浏览器中，你所引用的包，必须暴露出一个全局变量。如果没有，这种方式不适合在浏览器下使用，可以尝试**dll**的方式

  ```js
  externals:{
    react:'React',
    jquery:'jQuery'
  }
  // 或者
  externals:["React","jQuery"]
  ```

  - 不同模式下，value是不一样的。2，3模式下，是要引入全局变量，1模式是要加载包名。那如果这个包的包名和在浏览器下引入的全局变量一致，上面就可以写成一样了，比如moment
- externals和libraryTarget的关系
  - libraryTarget 配置如何暴露 library
    - 如果不设置library, 那这个library就不暴露, 就相当于一个自执行函数
  - externals 是决定的是以哪种模式去加载所引入的额外的包
  - libraryTarget决定了你的library运行在哪个环境，哪个环境也就决定了你哪种模式去加载所引入的额外的包。也就是说，externals应该和libraryTarget保持一致。library运行在浏览器中的，你设置externals的模式为- commonjs，那代码肯定就运行不了了
  - 如果是应用程序开发，一般是运行在浏览器环境libraryTarget可以不设置，externals默认的模式是global，也就是以全局变量的模式加载所引入外部的库