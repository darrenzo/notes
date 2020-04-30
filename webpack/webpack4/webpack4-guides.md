# webpack-guides

## 安装

### 本地安装

- 安装 webpack 4+ 版本
  - `npm install webpack webpack-cli --save-dev`
- 安装 4 以前的老版本
  - `npm install --save-dev webpack@<version>`
- 当你在本地安装 webpack 后，你能够从 node_modules/.bin/webpack 访问它的 bin 版本

### 全局安装（不推荐）

- `npm install --global webpack`
- 不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败

## 创建文件

```js
webpack-demo
|- package.json
|- package-lock.json
|- webpack.config.js
|- /dist
  |- bundle.js
  |- index.html
|- /src
  |- index.js
|- /node_modules
```

- package.json 文件，设置 `"private": true` 以便确保我们安装包是私有的(private)，并且移除 main 入口。这可以防止意外发布你的代码
- webpack 不会更改代码中除 import 和 export 语句以外的部分, 如果是用ES6，需要安装Babel
- `npx webpack --config webpack.config.js` 运行
  - 如果 webpack.config.js 存在，则 webpack 命令将默认选择使用它。使用 --config 选项可以传递任何名称的配置文件（用在设置package.json中的scripts属性）

## 常见步骤

### 项目初始化

```shell
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

### 设置**package.json**

- 添加 **"private": true** 和删除 **main**

### 安装loader

- loader数组的执行顺序是从右往左执行
- ['style-loader', 'css-loader'] 处理 css 文件
- ['style-loader', 'css-loader', 'less-loader'] 处理 less 文件
  - less-loader 内部依赖 less 包
- ['style-loader', 'css-loader', 'sass-loader'] 处理 sass、scss 文件
  - sass-loader 内部依赖 node-sass 包
  - node-sass包一般用npm装不成功，可以用cnpm
- **file-loader** 和 **url-loader** 处理图片 **png|svg|jpg|gif** 和字体 **woff|woff2|eot|ttf|otf**
  - url-loader 将html以及css中的图片(默认限制10KB以下的)打包成base64

  ```js
  {
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    include: [resolve('static'),resolve('src')], // 如果不指定检索的范围，则会进行全局搜索
    loader: 'url-loader',
    options: {
        limit: 100000,
        name: 'img/[name].[hash:7].[ext]'   // 在输出的目录中新建img文件夹，big放置打包好的文件
    }
  }
  ```

  - 我们的项目中有的图片列表是用v-for遍历出来的，也就是url放到了js中（vue文件的data里），这样就不能被base64编码了，url-loader只会去编译html以及css中的image。所以可以使用import图片的方式通过一个变量去接受，然后将这些变量放到v-for中去渲染

  ```js
  import user from '../static/user.png'

  dataAns: [
    {
      iconSrc: user
    }
  ]
  ```

- **xml-loader** 处理 xml 文件
- **csv-loader** 处理 csv 文件

### 安装插件

- **html-webpack-plugin** 自动生成index.html,里面自动生成对应的budle.js引入文件
- **clean-webpack-plugin** 自动清理 dist 目录

### 安装开发工具 devtool

- **source-map** 源文件与分发文件间的映射，比如 **inline-source-map**用在开发环境， **source-map** 用在生产环境

### 使用自动更新改动模式

- 自带的观察模式
  - **webpack --watch**，webpack会自动编译修改的文件而不用手动重新编译，但是需要手动刷新页面看效果
- **webpack-dev-server** 能够实时重新加载改动（自动刷新页面）
  - 启用命令行 `webpack-dev-server --open --config webpack.dev.js`

```js
devServer: {
  contentBase: './dist'
},
```

### 设置一个模拟服务器

- `npm install --save-dev express webpack-dev-middleware`
- output 项需要添加属性 **publicPath: '/'**
- 添加server.js文件

```js
const express = require('express');
const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');

const app = express();
const config = require('./webpack.config.js');
const compiler = webpack(config);

// Tell express to use the webpack-dev-middleware and use the webpack.config.js
// configuration file as a base.
app.use(webpackDevMiddleware(compiler, {
  publicPath: config.output.publicPath
}));

// Serve the files on port 3000.
app.listen(3000, function () {
  console.log('Example app listening on port 3000!\n');
});
```

- 运行 node server.js

### 调整文本编辑器

- 使用自动编译代码时，可能会在保存文件时遇到一些问题。某些编辑器具有“安全写入”功能，可能会影响重新编译
- 在一些常见的编辑器中禁用此功能：
  - Sublime Text 3
    - 在用户首选项(user preferences)中添加 atomic_save: "false"
  - IntelliJ
    - 在首选项(preferences)中使用搜索，查找到 "safe write" 并且禁用它
  - Vim
    - 在设置(settings)中增加 :set backupcopy=yes
  - WebStorm
    - 在 Preferences > Appearance & Behavior > System Settings 中取消选中 Use "safe write"

### 启用 HMR (自动更新改动的模块，不会刷新浏览器)

- 保证只有一个入口，否则HMR无效 ？？？？？
- **webpack.config.js** 的 **devServer** 添加 **hot: true**
- **webpack.config.js** 的 **plugins** 添加 **webpack.NamedModulesPlugin** 和 **webpack.HotModuleReplacementPlugin** 两个 webpack内置插件
  - 使用 **NamedModulesPlugin** 更容易查看要修补(patch)的依赖
- 在入口文件 **index.js** 中添加对子模块 **print.js** 的监听, 以便当 print.js 内部发生变更时可以告诉 webpack 接受更新的模块

```js
if (module.hot) {
  module.hot.accept('./print.js', function() {
    // 发生热替换时要执行的回调
  })
}
```

- 通过Node.js API 实现HMR

```js
// dev-server.js

const webpackDevServer = require('webpack-dev-server');
const webpack = require('webpack');

const config = require('./webpack.config.js');
const options = {
  contentBase: './dist',
  hot: true,
  host: 'localhost'
};
// 想要启用 HMR，还需要修改 webpack config，使其包含 HMR 入口起点
webpackDevServer.addDevServerEntrypoints(config, options);

const compiler = webpack(config);

// 不要将 dev server 选项放在 webpack config, 而是，在创建选项时，将其作为第二个参数传递
const server = new webpackDevServer(compiler, options);

server.listen(5000, 'localhost', () => {
  console.log('dev server listening on port 5000');
});
```

- HMR 修改 css
  - 可以通过 **style-loader** 达到热替换效果
  - 当更新 CSS 依赖模块时，此 **style-loader** 在后台使用 module.hot.accept 来修补 `<style>` 标签
- HMR功能相关的框架
  - [React Hot Loader](https://github.com/gaearon/react-hot-loader)：实时调整 react 组件
  - [Vue Loader](https://github.com/vuejs/vue-loader)：此 loader 支持用于 vue 组件的 HMR，提供开箱即用体验
  - [Redux HMR](https://survivejs.com/webpack/appendices/hmr-with-react/#configuring-hmr-with-redux)：无需 loader 或插件！只需对 main store 文件进行简单的修改

### HMR 可能出现的问题

- 页面上元素的绑定事件不会自动随热替换而进行更新
  - 需要在入口里面的**module.hot.accept**方法回调里进行预处理，每次页面重新渲染时，移除原元素，再重新渲染元素，重新绑定事件

### tree shaking (webpack 4+)

- 通常用于描述移除 JavaScript 上下文中的未引用代码
- 通过 package.json 的 "sideEffects" 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 "pure(纯的 ES2015 模块)"，由此可以安全地删除文件中未使用的部分
  - 「副作用」的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个 export 或多个 export。举例说明，例如 polyfill，它影响全局作用域，并且通常不提供 export
  - 这里的设置仅仅是找出哪些文件可以被安全的删除，真正删除需要压缩文件操作
  - 使用 ES2015 模块语法（即 import 和 export）才能使用 **tree shaking**
  
```js
// package.json
{
  "name": "your-project",
  // 表示整个项目文件都无副作用
  "sideEffects": false
}

// 或
{
  "name": "your-project",
  "sideEffects": [
    // 文件有副作用
    "./src/some-side-effectful-file.js",
    // 任何导入的文件都会受到 tree shaking 的影响
    // 这意味着，如果在项目中使用类似 css-loader 并导入 CSS 文件，则需要将其添加到 side effect 列表中，以免在生产模式中无意中将它删除
    '*.css'
  ]
}
```

### 压缩输出 （内置 uglifyjs 插件）

- 可以使用 -p(production) 这个 webpack 编译标记，来启用 uglifyjs 压缩插件 （启动生产环境就会进行压缩）
- **--optimize-minimize** 标记也会在 webpack 内部调用 UglifyJsPlugin
- 切换 mode 为 **production** （webpack 4+）

### 生产环境构建

- 在开发环境中
  - 我们需要具有强大的、具有实时重新加载或热模块替换能力的 source map 和 localhost server
- 在生产环境中
  - 我们的目标则转向于关注更小的 bundle，更轻量的 source map，以及更优化的资源，以改善加载时间
- 由于要遵循逻辑分离，我们通常建议为每个环境编写彼此独立的 webpack 配置，但为了遵循不重复原则，保留一个“通用”配置，通过 **webpack-merge** 工具将这些配置合并

```js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

- 指定环境
  - 使用 webpack 内置的 **DefinePlugin** 为所有的依赖定义这个变量(可以直接用来判断)，让各 library 使用内置的优化

  ```js
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
  ```

  - 无法在构建脚本 webpack.config.js 中，将 process.env.NODE_ENV 设置为 "production" ？？？？？？
    - 例如 `process.env.NODE_ENV === 'production' ? '[name].[hash].bundle.js' : '[name].bundle.js'` 这样的条件语句，在 webpack 配置文件中，无法按照预期运行

### 代码分离

- 入口起点： 最直接的通过创建多个入口实现模块分离，但存在一些问题：
  - 如果入口 chunks 之间包含重复的模块，那些重复模块都会被引入到各个 bundle 中
  - 这种方法不够灵活，并且不能将核心应用程序逻辑进行动态拆分代码

#### 防止重复的方法

- SplitChunksPlugin 内置插件 (webpack 4+): 提取各分块中公共的部分合并到一个文件中

```js
module.exports = {
  optimization: {
     splitChunks: {
        chunks: 'all'
     }
  }
}
```

- 4以前的老版本用的是 CommonsChunkPlugin 内置插件
- 推荐的其他插件
  - [mini-css-extract-plugin](https://webpack.js.org/plugins/mini-css-extract-plugin): 把CSS从主应用中分离出来
  - [bundle-loader](https://webpack.js.org/loaders/bundle-loader): 用来分离代码和懒加载输出的打包文件
  - [promise-loader](https://github.com/gaearon/promise-loader): 用 promise 实现 **bundle-loader** 一样的效果
  
#### 动态导入的方法

- 两种方法
  - 使用符合 ECMAScript 提案 的 import() 语法 （推荐）
    - import() 调用会在内部用到 promises
    - 旧版本浏览器不支持的话得引入polyfill库

    ```js
    import(/* webpackChunkName: "print" */ './print').then(module => {
            const print = module.default;

            print();
        });
    ```

  - 使用 webpack 特定的 require.ensure

#### bundle 分析

- [webpack-chart](https://alexkuz.github.io/webpack-chart/): webpack 数据交互饼图
- [webpack-visualizer](https://chrisbateman.github.io/webpack-visualizer/): 可视化并分析你的 bundle，检查哪些模块占用空间，哪些可能是重复使用的
- [webpack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer): 一款分析 bundle 内容的插件及 CLI 工具，以便捷的、交互式、可缩放的树状图形式展现给用户

### 懒加载

- 这种方式实际上是先把你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用或即将引用另外一些新的代码块
- 通过交互事件(如click)触发import模块进而动态导入实现懒加载

```js
button.onclick = e => import(/* webpackChunkName: "print" */ './print').then(module => {
  var print = module.default;

  print();
});
```

#### 框架上的使用

- [Code Splitting and Lazy Loading](https://reacttraining.com/react-router/web/guides/code-splitting): react
- [Lazy Load in Vue using Webpack's code splitting](https://alexjoverm.github.io/2017/07/16/Lazy-load-in-Vue-using-Webpack-s-code-splitting/): Vue
- [AngularJS + Webpack = lazyLoad](https://medium.com/@var_bin/angularjs-webpack-lazyload-bb7977f390dd): AngularJS

### 缓存

- 通过 **output.filename** 属性设置输出文件格式为 `[name].[contenthash].js`
  - 根据文件的内容生成hash
- 通过**optimization.runtimeChunk**将运行时代码分割成单独的块。将其设置为single，以便为所有块创建一个运行时包
- 提取第三方库模块(基本不会进行更新)到单独的vendors文件

```js
// 会生成 main wendor runtime 文件
optimization: {
  runtimeChunk: 'single',
  splitChunks: {
    cacheGroups: {
      vendor: {
        test: /[\\/]node_modules[\\/]/, ？？？？？
        name: 'vendors',
        chunks: 'all'
      }
    }
  }
}
```

- 同样的文件连续2次构建会导致hash不一致，因为这三种bundle hash改变的原理中 vendor 的原理不符合
  - **main**: bundle 会随着自身的新增内容的修改，而发生变化
  - **runtime**: bundle 会因为当前包含一个新模块的引用，而发生变化
  - **vendor**: bundle 会随着自身的 module.id 的修改，而发生变化(每个模块。默认情况下，id根据解析顺序递增)
- 修复 vendor bundle hash 变化问题
  - new webpack.NamedModulesPlugin()：将使用模块的路径，而不是数字标识符。此插件有助于在开发过程中输出结果的可读性，然而执行时间会长一些，用在开发环境
  - new webpack.HashedModuleIdsPlugin()：用于生产环境构建

### [创建一个library](https://webpack.js.org/guides/author-libraries#authoring-a-library)

### [shimming](https://webpack.js.org/guides/shimming)

- 使用shimming的情景
  - 第三方库需要引用一些全局依赖(如jQuery中的$): 但不推荐使用全局的东西
  - 使用兼容库(如polyfill)

### 渐进式网络应用程序

- 离线时通过 [Service Workers](https://developers.google.com/web/fundamentals/primers/service-workers/) 运行应用程序
- npm install http-server --save-dev
- package.json中添加命令 "build": "webpack"和"start": "http-server dist"
- 运行 npm run build 和 npm start
- `http://localhost:8080 (i.e. http://127.0.0.1)` 可以看到应用程序运行
- Workbox webpack plugin
  - npm install workbox-webpack-plugin --save-dev
  
  ```js
  const WorkboxPlugin = require('workbox-webpack-plugin');
  module.exports = {
    plugins: [
      new WorkboxPlugin.GenerateSW({
       clientsClaim: true,
       skipWaiting: true
     })
    ]
  }
  ```

- 注册 Service Worker

```js
// index.js
import _ from 'lodash';
import printMe from './print.js';

if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('/service-worker.js').then(registration => {
      console.log('SW registered: ', registration);
    }).catch(registrationError => {
      console.log('SW registration failed: ', registrationError);
    });
  });
}
```

- npm run build
- npm start 再次运行服务
- 此时停止 http 服务，刷新网页，应用程序在离线的情况下依旧运行

### TypeScript

- npm install --save-dev typescript ts-loader
- tsconfig.json 配置
- webpack.config.js配置

```js
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    extensions: [ '.tsx', '.ts', '.js' ]
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

- source map
  - tsconfig.json开启 "sourceMap": true
  - webpack.config.js开启 devtool: 'source-map'
- 引入第三方库
  - 一定要牢记同时安装这个库的类型声明文件
    - 例如 `npm install --save-dev @types/lodash`
- 导入其他资源
  - 要在 TypeScript 里使用非代码资源，我们需要告诉 TypeScript 如何兼容这些导入类型

```js
// 编写自定义的类型声明,将 .svg 文件进行声明设置
// custom.d.ts
declare module "*.svg" {
  const content: any;
  export default content;
}
```

### 环境变量

- 命令行设置环境变量
  - `webpack --env.NODE_ENV=local --env.production --progress`
- webpack.config.js引用env变量，需要把**module.exports**改成函数

```js
const path = require('path');

module.exports = env => {
  console.log('NODE_ENV: ', env.NODE_ENV); // 'local'
  console.log('Production: ', env.production); // true

  return {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
};
```

### 构建性能

- 使用最新的webpack版本、node、package
- loaders
  - 应用于所需的最少模块
    - 使用include仅将 loader 模块应用在实际需要用其转换的位置中

    ```js
    {
      test: /\.js$/,
      include: path.resolve(__dirname, "src"),
      loader: "babel-loader"
    }
    ```

- 每个额外的 loader/plugin 都有启动时间。尽量少使用不同的工具
- 提高解析速度的方法：
  - 尽量减少 **resolve.modules**, **resolve.extensions**, **resolve.mainFiles**, **resolve.descriptionFiles** 中类目的数量，因为他们会增加文件系统调用的次数
  - 如果你不使用 **symlinks** (例如 npm link 或者 yarn link)，可以设置 resolve.symlinks: false
  - 如果你使用自定义解析 plugins ，并且没有指定 context 信息，可以设置 resolve.cacheWithContext: false
  - 使用 **DllPlugin** 将更改不频繁的代码进行单独编译。这将改善引用程序的编译速度，即使它增加了构建过程的复杂性
- 减少编译的整体大小，以提高构建性能
  - 使用 更少/更小 的库
  - 在多页面应用程序中使用 SplitChunksPlugin
  - 在多页面应用程序中以 async 模式使用 SplitChunksPlugin ？？？？？
  - 移除不使用的代码
  - 只编译你当前正在开发部分的代码
- **thread-loader** 可以将非常消耗资源的 loaders 转存到 **worker pool** 中
  - 不要使用太多的 workers ，因为 Node.js 的 runtime 和 loader 有一定的启动开销。最小化 workers 和主进程间的模块传输。进程间通讯(IPC)是非常消耗资源的
- 使用 **cache-loader** 启用持久化缓存。使用 package.json 中的 "postinstall" 清除缓存目录
  - 在一些性能开销较大的 loader 之前添加此 loader，以将结果缓存到磁盘里。（保存和读取这些缓存文件会有一些时间开销，所以请只对性能开销较大的 loader 使用此 loader）

  ```js
  module.exports = {
    module: {
      rules: [
        {
          test: /\.ext$/,
          use: [
            'cache-loader',
            ...loaders
          ],
          include: path.resolve('src')
        }
      ]
    }
  }
  ```

- TypeScript
  - ts-loader默认没有增量编译，并且单线程，直接进行类型检查。这会导致TypeScript的编译过程非常慢
  - 使用**happypack(或thread-loader) + cache-loader(或hard-source-webpack-plugin) + ts-loader + fork-ts-checker-webpack-plugin**来加速编译
    - 这几种方式都需要将TypeScript代码从.vue文件中移出来
    - ts-loader要设置options: { happyPackMode: true, transpileOnly: true }
      - 如果开启transpileOnly, webpack 4 会报 “"export not found"” 警告，可以忽略

      ```js
      module.exports = {
        ...
        stats: {
          warningsFilter: /export .* was not found in/
        }
      }
      ```

    - new ForkTsCheckerWebpackPlugin({ checkSyntacticErrors: true }) 插件默认只检查语义错误，需要添加检查语法错误
- Sass
  - **node-sass** 中有个来自 Node.js 线程池的阻塞线程的 bug
  - 当使用 **thread-loader** 时，需要设置 **workerParallelJobs: 2**
    - 把这个 loader 放置在其他 loader 之前， 放置在这个 loader 之后的 loader 就会在一个单独的 worker 池(worker pool)中运行

    ```js
    use: [
      "thread-loader",
      "expensive-loader"
    ]
    // 或者

    use: [
      {
        loader: "thread-loader",
        // 有同样配置的 loader 会共享一个 worker 池(worker pool)
        options: {
          // 一个 worker 进程中并行执行工作的数量
          // 默认为 20
          workerParallelJobs: 50,
        }
      },
      "expensive-loader"
    ]
    ```

#### 开发阶段

- 增量构建
  - 使用 webpack 的监听模式
    - 不要使用其他工具来监听你的文件和调用 webpack
    - 在监听模式下构建会记录时间戳并将信息传递给编译让缓存失效
  - 在某些设置中，监听会回退到轮询模式
    - 有许多监听文件会导致 CPU 大量负载。在这些情况下，你可以使用 **watchOptions.poll** 来增加轮询的间隔
- 在内存中编译
  - 通过在内存中进行代码的编译和资源的提供，但并不写入磁盘来提高性能
    - **webpack-dev-server**、**webpack-hot-middleware**、**webpack-dev-middleware**
- Devtool 设置
  - 不同的 devtool 的设置，会导致不同的性能差异
    - "eval" 具有最好的性能，但并不能帮助你转译代码
    - 如果你能接受稍差一些的 mapping 质量，可以使用 cheap-source-map 选项来提高性能
    - 使用 eval-source-map 配置进行增量编译
    - 在大多数情况下，cheap-module-eval-source-map 是最好的选择
- 避免使用在生产环境下才会用到的工具
  - UglifyJsPlugin
  - ExtractTextPlugin
  - [hash]/[chunkhash]
  - AggressiveSplittingPlugin
  - AggressiveMergingPlugin
  - ModuleConcatenationPlugin
- 最小化入口chunk ？？？？？
  - webpack 只会在文件系统中生成已经更新的 chunk
  - 对于某些配置选项(HMR, [name]/[chunkhash] in output.chunkFilename, [hash])来说，除了更新的 chunks 无效之外，入口 chunk 也不会生效
  - 应当在生成入口 chunk 时，尽量减少入口 chunk 的体积，以提高性能
  
  ```js
  // 下述代码块将只提取包含 runtime 的 chunk ，其他 chunk 都作为子模块
  new CommonsChunkPlugin({
    name: "manifest",
    minChunks: Infinity
  })
  ```

- 减少额外的优化开销
  - webpack做额外的算法工作来优化输出的大小和负载性能
  - 以下这些优化是针对较小代码库的性能，但在较大的代码库中可能代价高昂

  ```js
  module.exports = {
    // ...
    optimization: {
      removeAvailableModules: false,
      removeEmptyChunks: false,
      splitChunks: false,
    }
  };
  ```

- webpack能够在输出包中生成路径信息。然而，这给打包了数千个模块的项目带来了垃圾收集的压力。在options.output.pathinfo 中关闭此选项设置

```js
module.exports = {
  // ...
  output: {
    pathinfo: false
  }
};
  ```

- 使用node的 8.9.x 版本(性能问题存在于8.9.10 - 9.11.1之间)
  - 在Node.js及其ES2015 Map和Set实现的最新稳定版本中，性能有所下降。修复程序已经合并到master中，但是还没有发布版本。与此同时，要最大限度地提高构建速度，请尝试使用8.9.x版本。webpack已经开始大量使用这些ES2015数据结构，它还将改进初始构建时间

- TypeScript Loader
  - 最近，ts-loader开始使用内部的 TypeScript 观察模式的Api，这大大减少了每次迭代要重新构建的模块数量
  - 开启 **experimentalWatchApi**, 这个**experimentalWatchApi** 本身与普通的TypeScript观察模式具有相同的逻辑，对于开发使用非常稳定

  ```js
  module.exports = {
    // ...
    test: /\.tsx?$/,
    use: [
      {
        loader: 'ts-loader',
        options: {
          // vue 单文件组件中假如使用了lang="ts"，ts-loader需要配置appendTsSuffixTo: [/\.vue$/]，用来给.vue文件添加个.ts后缀用于编译。这个.ts文件并不实际存在，所以在使用cache-loader时，会报找不到这个文件的错误
          // 把 TypeScript代码 从 .vue中移出来。使用一个单独的ts文件，然后vue在引用这个ts文件
          appendTsSuffixTo: [/\.vue$/], // 属性值是要与文件名匹配的正则表达式列表，如果文件名与其中一个正则表达式匹配，则将在文件名后面附加一个.ts后缀。 (附加.tsx后缀的属性是appendTsxSuffixTo)

          transpileOnly: true,
          experimentalWatchApi: true,
        },
      },
    ],
  };
  ```

  - **ts-loader**文档建议使用**cache-loader**，但这实际上降低了磁盘写操作的增量构建速度
  - 在**ts-loader** github存储库中有一个完整的示例[full example](https://github.com/TypeStrong/ts-loader/tree/master/examples/fork-ts-checker-webpack-plugin)

### 生产环境

- 多个编译
  - [parallel-webpack](https://github.com/trivago/parallel-webpack): 它允许编译工作在 worker 池中进行
  - cache-loader: 缓存可以在多个编译时之间共享
- Source Maps 真的非常消耗资源
- Babel：项目中的 preset/plugins 数量最小化

### 内容安全策略

- webpack 能够为其加载的所有脚本添加 **nonce**
- 在引入的入口脚本中设置一个 **__webpack_nonce__** 变量
  - 应该为每个唯一的页面视图生成和提供一个唯一的基于 hash 的 nonce，这就是为什么 __webpack_nonce__ 要在入口文件中指定，而不是在配置中指定的原因
- **nonce** 应该是一个 base64 编码的字符串

```js
// 在 entry 文件中：
__webpack_nonce__ = 'c29tZSBjb29sIHN0cmluZyB3aWxsIHBvcCB1cCAxMjM=';
```

- 启用CSP
  - CSP 默认情况下不启用
  - 文档需要与一个CSP header或者meta标签`<meta http-equiv="Content-Security-Policy" ...>`一起发送以告知浏览器启用CSP

  ```html
  <!-- 包含 CDN 白名单 URL的 CSP header -->
  Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.cdn.com;
  ```

### 开发-Vagrant

- 如果你在开发一个更高级的项目，并且使用 Vagrant 来实现在虚拟机)上运行你的开发环境，那么你或许需要在虚拟机上运行 webpack
- [相关步骤](https://www.webpackjs.com/guides/development-vagrant/)

### [管理依赖](https://www.webpackjs.com/guides/dependency-management/) ？？？？

### [公共路径](https://www.webpackjs.com/guides/public-path/) ？？？？？

- webpack 提供一个非常有用的配置，该配置能帮助你为项目中的所有资源指定一个基础路径

### 插件

- extract-text-webpack-plugin
  - webpack3使用
  - webpack4使用 mini-css-extract-plugin
  - 主要是为了抽离css样式,防止将样式打包在js中引起页面样式加载错乱的现象
  - 这会使用的CSS文件无法使用HMR
- copy-webpack-plugin
  - 将单个文件或整个目录复制到构建目录
- babili-webpack-plugin
  - 基于 babel 的 minifier
  - babel-minify-webpack-plugin  ？？？