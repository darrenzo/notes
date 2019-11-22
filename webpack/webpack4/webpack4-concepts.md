# webpack-concepts

- [英文网](https://webpack.js.org/concepts/)
- [中文网](https://www.webpackjs.com/concepts/)

## 概要

- 版本4.0.0后,webpack不需要配置文件来打包您的项目，但是它更好地满足了需求
- [概念术语](https://www.webpackjs.com/glossary/)

## Mode(模式)

- 提供 **mode** 配置选项，告知 webpack 使用相应模式的内置优化
  - 属性值为 production | development | none
  - 默认是 production
- 可以在配置中设置

```js
module.exports = {
  mode: 'production'
};
```

- 也可以从 CLI 参数中传递
  - `webpack --mode=production`

- development 模式
  - webpack.development.config.js
  - 会将 process.env.NODE_ENV 的值设为 development
  - 启用 NamedChunksPlugin 和 NamedModulesPlugin
- production 模式
  - webpack.production.config.js
  - 会将 process.env.NODE_ENV 的值设为 production
  - 启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin
- 只设置 NODE_ENV，则不会自动设置 mode

## 工作流

![webpack-workflow](/img/webpack-workflow.png)

## 入口

- **entry**属性指示webpack应该使用哪个模块开始构建其内部依赖关系图
- webpack将找出入口所依赖的其他模块和库(直接和间接)
- 动态加载的模块不是入口起点
- 入口文件默认是 **./src/index.js**，可以通过在webpack配置中配置entry属性来指定不同的(或多个入口)

### single entry syntax

- `entry: string|Array<string>`

```js
// webpack.config.js
module.exports = {
  entry: './path/to/my/entry/file.js'
};

// 或者

module.exports = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```

- 此时chunk 会被命名为 main
- 将文件路径数组传递给entry属性将创建“多个主入口”，数组的每一项都会执行。在你想要多个依赖文件一起注入，并且将它们的依赖导向到一个“chunk”时，传入数组的方式就很有用 ？？？？

### 对象语法

- `entry: {[entryChunkName: string]: string|Array<string>}`
- 每个键(key)会是 chunk 的名称，该值描述了 chunk 的入口起点
- 这是在定义入口时最具扩展性的写法
  - 可重用并且可以与其他配置组合使用
  - 用于将关注点从环境、构建目标、运行时中分离, 然后使用专门的工具（如 webpack-merge）将它们合并

```js
module.exports = {
  entry: {
    app: './src/app.js',
    adminApp: './src/adminApp.js'
  }
};
```

### 动态入口

```js
entry: () => './demo'

// 或

entry: () => new Promise((resolve) => resolve(['./demo', './demo2']))

// 当结合 output.library 选项时：如果传入数组，则只导出最后一项
```

### 常见场景

#### 分离 应用程序(app) 和 第三方库(vendor) 入口

```js
// webpack.config.js
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```

- webpack 从 app.js 和 vendors.js 开始创建依赖图。这些依赖图是彼此完全分离、互相独立的（每个 bundle 中都有一个 webpack 引导）
- 这种方式比较常见于，只有一个入口起点（不包括 vendor）的单页应用(SPA)程序中
- **webpack 4** 为了支持提供更佳 vendor 分离能力的 **DllPlugin**，考虑移除该场景

#### 多页面应用程序

```js
const config = {
  entry: {
    // 3 个独立分离的依赖图
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

- 在多页应用（MPA）中，（每当页面跳转时）服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载, 这给了我们特殊的机会去做很多事
  - 使用 **SplitChunksPlugin** (weboack 3 是 **CommonsChunkPlugin**) 为每个页面间的应用程序共享代码创建 bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益
  - 每个 HTML 文档只使用一个入口起点

## 出口

- **output**属性告诉webpack在何处输出它创建的包，以及如何命名这些文件
- 出口文件默认是 `./dist/main.js`
- `./dist` 文件夹是默认的打包输出目标文件
- 当有多个entry时，只能指定一个 output 配置

```js
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    // 配置标明将输出单个文件 my-first-webpack.bundle.js 到 dist 目录
    filename: 'my-first-webpack.bundle.js'
  }
};
```

### 多入口时配置output

- 用变量的形式为每个入口文件指定唯一的出口文件
- 使用入口名称
  - **filename: "[name].bundle.js"**
- 使用内部 chunk id
  - **filename: "[id].bundle.js"**
- 使用每次构建过程中，唯一的 hash 生成
  - **filename: "[name].[hash].bundle.js"**
- 使用基于每个 chunk 内容的 hash
  - **filename: "[chunkhash].bundle.js"**
- 使用基于每个提取出的内容的hash
  - **filename: "[contenthash].bundle.css"**
- 使用函数生成
  
  ```js
  filename: (chunkData) => {
    return chunkData.chunk.name === 'main' ? '[name].js': '[name]/[name].js';
  }
  ```

```js
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
};

// writes to disk: ./dist/app.js, ./dist/search.js
```

### chunkFilename

- 此选项决定了非入口(non-entry) chunk 文件的名称
- 取值可选项与**output.filename**一样
- 这些文件名需要在 runtime 根据 chunk 发送的请求去生成 ？？？？
- 需要在 webpack runtime 输出 bundle 值时，将 chunk id 的值对应映射到占位符(如 [name] 和 [chunkhash])。这会增加文件大小，并且在任何 chunk 的占位符值修改后，都会使 bundle 失效 ？？？？
- 如果不设置，默认使用从 output.filename 中推断出的值，如果设置，则按设置的格式。（ chunkFilename 的 [name] 都会被预先替换为 [id]，除非在动态导入时特别设置）

```js
function getComponent() {
    // 以下的注释就是设置lodash生成单个bundle时，文件名避免变成[id]格式
    return import(/* webpackChunkName: "lodash" */ 'lodash').then(({
        default: _
    }) => {
        const element = document.createElement('div');

        element.innerHTML = _.join(['Hello', 'webpack'], ' ');

        return element;
    }).catch(error => 'An error occurred');
}
```

### CDN 和 资源 hash 的复杂示例

```js
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

- 在编译时不知道最终输出文件的 publicPath 的情况下，publicPath 可以留空，并且在入口起点文件运行时动态设置
  - 如果你在编译时不知道 publicPath，你可以先忽略它，并且在入口起点设置 **__webpack_public_path__**

  ```js
  // 相当于对文件中的相对路径补全为绝对路径
  __webpack_public_path__ = myRuntimePublicPath
  ```

## Loaders

- webpack 只能理解 js 和 JSON 文件
- **Loaders** 允许 webpack 操作其他类型的文件并把它们转成有效的模块
- Loaders有两个属性可用来配置
  - **test**: 指定哪类文件需要被转换
  - **use**: 指定哪个loader要被用来协助转换
- 还有preLoaders 和 postLoaders, 执行顺序为 preLoaders - loaders - postLoaders

```js
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
};
```

- loader除了配置写法，还有内联和CLI命令行的写法，但不推荐
- loader 特性
  - loader 支持链式传递。能够对资源使用流水线(pipeline)。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript
  - loader 可以是同步的，也可以是异步的
  - loader 运行在 Node.js 中，并且能够执行任何可能的操作
  - loader 接收查询参数。用于对 loader 传递配置
  - loader 也能够使用 options 对象进行配置
  - 除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段
  - loader 能够产生额外的任意文件

## Plugins

- [list of plugins](https://webpack.js.org/plugins)
- 插件目的在于解决 loader 无法实现的其他事
- 插件需要添加到 **plugins** 数组中
- 为了可以在配置中多次使用插件来实现不同的目的，所以需要通过使用**new**操作符调用它来创建插件的实例

```js
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```

- webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问

```js
// plugin文件
class BasicPlugin{
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options){
  }

  // Webpack 会调用 BasicPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler){
    compiler.plugin('compilation',function(compilation) {
    })
  }
}

// 导出 Plugin
module.exports = BasicPlugin;


// webpack.config.js
const BasicPlugin = require('./BasicPlugin.js');
module.export = {
  plugins:[
    new BasicPlugin(options),
  ]
}
```

- Node API
  - 即便使用 Node API，用户也应该在配置中传入 plugins 属性。compiler.apply 并不是推荐的使用方式

```js
  const webpack = require('webpack'); //访问 webpack 运行时(runtime)
  const configuration = require('./webpack.config.js');

  let compiler = webpack(configuration);
  compiler.apply(new webpack.ProgressPlugin());

  compiler.run(function(err, stats) {
    // ...
  });
```

## 模块解析

- 当打包模块时，webpack 使用 enhanced-resolve 来解析文件路径
  - 绝对路径, 相对路径, 模块路径

- 模块路径
  - 模块将在 resolve.modules 中指定的所有目录内搜索。 你可以替换初始模块路径，此替换路径通过使用 resolve.alias 配置选项来创建一个别名

```js
import "module";
import "module/lib/file";
```

- import()参数webpack 4 的改变

```js
// 当在被导入的模块中使用cjs写法
module.exports = 42;

// webpack 4 则在导入的模块中要使用 default

// webpack 3
42

// webpack 4

{deafult: 42}
```

```js
// webpack 3
import(/* webpackChunkName: "demo" */ 'demo').then(
    _ => {
      var element = document.createElement('div');
      element.innerHTML = _.join(['Hello', 'webpack'], ' ');
    }
).catch(error => 'An error occurred');

// webpack 4
import(/* webpackChunkName: "demo" */ 'demo').then(
    ( { default: _ } ) => {
        const element = document.createElement('div');
        element.innerHTML = _.join(['Hello', 'webpack'], ' ');
    }
).catch(error => 'An error occurred');
```

```js
// 当调用 ES6 模块的 import() 方法（引入模块）时，必须指向模块的 .default 值，因为它才是 promise 被处理后返回的实际的 module 对象
import(/* webpackChunkName: "print" */ './print').then(module => {
        const print = module.default;

        print();
    });
```

- 解析路径后，解析器(resolver)将检查路径是否指向文件或目录
  - 路径指向一个文件
    - 如果路径具有文件扩展名，则被直接将文件打包
    - 否则，将使用 resolve.extensions 选项作为文件扩展名来解析，此选项告诉解析器在解析中能够接受哪些扩展名（例如 .js, .jsx）
  - 路径指向一个文件夹, 则采取以下步骤找到具有正确扩展名的正确文件
    - 如果文件夹中包含 package.json 文件，则按照顺序查找 resolve.mainFields 配置选项中指定的字段, 并且根据 package.json 中的第一个这样的字段确定文件路径
    - 如果 package.json 文件不存在或者 package.json 文件中的 main 字段没有返回一个有效路径，则按照顺序查找 resolve.mainFiles 配置选项中指定的文件名，看是否能在 import/require 目录下匹配到一个存在的文件名
    - 文件扩展名通过 resolve.extensions 选项采用类似的方法进行解析

### 解析 Loader

- Loader 解析遵循与文件解析器指定的规则相同的规则
- resolveLoader 配置选项可以用来为 Loader 提供独立的解析规则

### 缓存

- 每个文件系统访问都被缓存，以便更快触发对同一文件的多个并行或串行请求
- 在观察模式下，只有修改过的文件会从缓存中摘出。如果关闭观察模式，在每次编译前清理缓存

## Runtime 和 Manifest

- 在使用 webpack 构建的典型应用程序或站点中，有三种主要的代码类型
  - 自己编写的源码
  - 源码依赖的第三方库
  - webpack 的 runtime 和 manifest，管理所有模块的交互
- runtime，以及伴随的 manifest 数据，主要是指：在浏览器运行时，webpack 用来连接模块化的应用程序的所有代码
  - runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑
  - 当编译器开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "Manifest"，当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块
  - 无论你选择哪种模块语法，那些 import 或 require 语句现在都已经转换为 **__webpack_require__** 方法，此方法指向模块标识符。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块
  - 通过使用 bundle 计算出 hash 作为文件名称，这样在内容或文件修改时，浏览器中将通过新的 hash 指向新的文件，从而使缓存无效。可是即使表面上某些内容没有修改，计算出的哈希还是会改变。这是因为，runtime 和 manifest 的注入在每次构建都会发生变化

## 构建目标（target）

- 每个target都有各种部署/环境特定的附加项，以支持满足其需求

```js
// webpack.config.js
// 使用 node, webpack 会编译为用于「类 Node.js」环境（使用 Node.js 的 require ，而不是使用任意内置模块（如 fs 或 path）来加载 chunk）
module.exports = {
  target: 'node'
};
```

- 尽管 webpack 不支持向 target 传入多个字符串，你可以通过打包两份分离的配置来创建同构的库

```js
// webpack.config.js
var path = require('path');
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};

var clientConfig = {
  target: 'web', // <=== 默认是 'web'，可省略
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];

// 将在 dist 文件夹下创建 lib.js 和 lib.node.js 文件
```

- 构建例子： [Boilerplate of Electron-React Application](https://github.com/chentsulin/electron-react-boilerplate): 一个 electron 主进程和渲染进程构建过程的很好的例子

## 模块热替换

- HMR - Hot Module Replacement: 模块热替换功能会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面
  - 保留在完全重新加载页面时丢失的应用程序状态
  - 只更新变更内容，以节省宝贵的开发时间
  - 调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式
- 对于多数实现来说，webpack-dev-server 能够配合良好，可以让你快速入门 HMR
  - webpack-dev-server 支持 hot 模式，在试图重新加载整个页面之前，热模式会尝试使用 HMR 来更新

### 在应用程序中

- 通过以下步骤，可以做到在应用程序中置换模块
- 你可以设置 HMR，以使此进程自动触发更新，或者你可以选择要求在用户交互时进行更新

![webpack-hmr](/img/webpack-HMR.png)

### 在编译器中

- 除了普通资源，编译器需要发出 "update"，以允许更新之前的版本到新的版本
- "update" 由两部分组成：
  - 更新后的 manifest (JSON)
    - manifest 包括新的编译 hash 和所有的待更新 chunk 目录
  - 一个或多个更新后的 chunk (JavaScript)
    - 每个更新 chunk 都含有对应于此 chunk 的全部更新模块（或一个 flag 用于表明此模块要被移除）的代码
- 编译器确保模块 ID 和 chunk ID 在这些构建之间保持一致
  - 通常将这些 ID 存储在内存中（例如，使用 webpack-dev-server 时），但是也可能将它们存储在一个 JSON 文件中
  
### 在模块中

- HMR 是可选功能，只会影响包含 HMR 代码的模块
- 在多数情况下，不需要强制在每个模块中写入 HMR 代码。如果一个模块没有 HMR 处理函数，更新就会冒泡
  - 这意味着一个简单的处理函数能够对整个模块树进行更新。如果在这个模块树中，一个单独的模块被更新，那么整组依赖模块都会被重新加载

### 在 HMR Runtime 中

- 对于模块系统的 runtime，附加的代码被发送到 parents 和 children 跟踪模块
- 在管理方面，runtime 支持两个方法 check 和 apply
  - check 发送 HTTP 请求来更新 manifest
    - 如果请求失败，说明没有可用更新。如果请求成功，待更新 chunk 会和当前加载过的 chunk 进行比较
    - 对每个加载过的 chunk，会下载相对应的待更新 chunk
    - 当所有待更新 chunk 完成下载，就会准备切换到 ready 状态
  - apply 方法将所有被更新模块标记为无效
    - 对于每个无效模块，都需要在模块中有一个更新处理函数，或者在它的父级模块们中有更新处理函数，否则，无效标记冒泡，并也使父级无效
    - 每个冒泡继续，直到到达应用程序入口起点，或者到达带有更新处理函数的模块（以最先到达为准，冒泡停止）。如果它从入口起点开始冒泡，则此过程失败
  - 之后，所有无效模块都被（通过 dispose 处理函数）处理和解除加载。然后更新当前 hash，并且调用所有 "accept" 处理函数。runtime 切换回闲置状态(idle state)，一切照常继续