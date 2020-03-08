# gulp4

- [官网](https://www.gulpjs.com.cn/docs/getting-started/quick-start/)

## 安装

- 如果之前安装过**gulp**，可以执行 `npm rm --global gulp` 来移除旧的安装
  - gulp@4 版本以下不兼容高版本的nodejs，建议使用 nodejs@10 左右的，比如 10.15.3
- 通过 `npm install --global gulp-cli` 安装新的 CLI， `gulp -v` 检查**gulp**版本
  - 必须安装，否则命令行无法使用gulp命令
- 在项目文件夹下 `npm init` 初始化后，`npm install --save-dev gulp` 安装**gulp**依赖

## gulpfile.js

- **gulpfile.js**文件是编写具体gulp任务的地方
- **gulpfile.js**文件可以用预编译语言，例如 **TypeScript** 和 **Babel**
  - 如果用 TypeScript，则需要把 **gulpfile.js** 改名为 **gulpfile.ts**，并安装 **ts-node** 模块
  - 如果用 Babel，则需要把 **gulpfile.js** 改名为 **gulpfile.babel.js**，并安装 **@babel/register** 模块
- Node的模块解析允许使用名为gulpfile.js的目录替换gulpfile.js文件，该目录包含一个index.js文件，该文件被视为gulpfile.js。然后，该目录可以包含用于任务的各个模块。如果使用别的语言编写，请相应地命名文件夹和文件

## 导出任务

- 公开任务
  - 被export，可以通过 gulp 命令直接调用
- 私有任务
  - 内部使用，通常作为 series() 或 parallel() 组合的组成部分

```js
const { series } = require('gulp');

// 公开任务，可被gulp命令直接调用
function build(cb) {
  // body omitted
  cb();
}

// 私有任务，只在内部使用
function bundle(cb) {
  // body omitted
  cb();
}

exports.build = build;
exports.default = series(transpile, bundle);

// gulp <task> <task> <task>... 格式
// gulp build 执行build任务，不带build参数默认执行default任务
```

## 组合任务

- series(task1, task2, ...)
  - 顺序执行, 其中一个报错，整个任务组立即停止，不再继续
- parallel(task1, task2, ...)
  - 并发执行，其中一个报错，整个任务组立即停止，不再继续，其他任务完成情况不确定

```js
// 可以进行判断，且可以互相无限制嵌套
if (process.env.NODE_ENV === 'production') {
  exports.build = series(transpile, minify);
} else {
  exports.build = series(
      transpile,
      parallel(
        cssTranspile,
        series(jsTranspile, jsBundle)
      ),
      parallel(cssMinify, jsMinify),
      publish
    );
}
```

- 当一个组合操作执行时，这个组合中的每一个任务每次被调用时都会被执行
  - 每个组合任务中最好保证每个单一任务在任务组中只被执行一次

```js
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

// 多次执行clean, 应该把 后两个任务中的clean去掉，series(clean, parallel(css, javascript))
exports.build = parallel(css, javascript);
```

## 异步执行

- 任务完成通知
  - 当从任务（task）中返回 stream、promise、event emitter、child process 或 observable 时，成功或错误值将通知 gulp 是否继续执行或结束。如果任务（task）出错，gulp 将立即结束执行并显示该错误

```js
// 返回 stream  ????
const { src, dest } = require('gulp');

function streamTask() {
  return src('*.js')
    .pipe(dest('output'));
}

exports.default = streamTask;

// 返回 promise
function promiseTask() {
  return Promise.resolve('the value is ignored');
}

exports.default = promiseTask;

// 返回 event emitter
const { EventEmitter } = require('events');

function eventEmitterTask() {
  const emitter = new EventEmitter();
  // Emit has to happen async otherwise gulp isn't listening yet
  setTimeout(() => emitter.emit('finish'), 250);
  return emitter;
}

exports.default = eventEmitterTask;

// 返回 child process
const { exec } = require('child_process');

function childProcessTask() {
  return exec('date');
}

exports.default = childProcessTask;

// 返回 observable
const { Observable } = require('rxjs');

function observableTask() {
  return Observable.of(1, 2, 3);
}

exports.default = observableTask;
```

- 使用 callback
  - 如果任务（task）不返回任何内容，则必须使用 callback 来指示任务已完成

  ```js
  function callbackTask(cb) {
    // `cb()` should be called by some async work
    cb();
  }

  exports.default = callbackTask;
  ```

  - 如需通过 callback 把任务（task）中的错误告知 gulp，请将 Error 作为 callback 的唯一参数

  ```js
  function callbackError(cb) {
    // `cb()` should be called by some async work
    cb(new Error('kaboom'));
  }

  exports.default = callbackError;
  ```

- 通常会将此 callback 函数传递给另一个 API ，而不是自己调用它

```js
const fs = require('fs');

function passingCallback(cb) {
  fs.access('gulpfile.js', cb);
}

exports.default = passingCallback;
```

- 使用 async/await
  - [完整案例](https://blog.csdn.net/weixin_33813128/article/details/91444400)

```js
const fs = require('fs');

async function asyncAwaitTask() {
  const { version } = fs.readFileSync('package.json');
  console.log(version);
  await Promise.resolve('some result');
}

exports.default = asyncAwaitTask;
```

- gulp 不再支持同步任务
  - 每个task必须是异步任务（必须使用前面提到的返回方式）
  - 出现"Did you forget to signal async completion?"时就需要使用以上返回方式解决

## 处理文件

### src(globs, [options])、dest()、pipe()

- gulp 暴露了 src() 和 dest() 方法用于处理计算机上存放的文件
  - src()：接受 glob 参数，并从文件系统中读取文件然后生成一个 Node 流（stream）
    - 它将所有匹配的文件读取到内存中并通过流（stream）进行处理
    - 由 src() 产生的流（stream）应当从任务（task）中返回并发出异步完成的信号
    - 流（stream）所提供的主要的 API 是 .pipe() 方法，用于连接转换流（Transform streams）或可写流（Writable streams）
  - dest() 接受一个输出目录作为参数，并且它还会产生一个 Node 流（stream），通常作为终止流（terminator stream）
    - 当它接收到通过管道（pipeline）传输的文件时，它会将文件内容及文件属性写入到指定的目录中
    - gulp 还提供了 symlink() 方法，其操作方式类似 dest()，但是创建的是链接而不是文件（ 详情请参阅 symlink() ）

  ```js
  const { src, dest } = require('gulp');
  const babel = require('gulp-babel');

  exports.default = function() {
    return src('src/*.js')
      .pipe(babel())
      .pipe(dest('output/'));
  }
  ```

- 利用 .pipe() 方法将插件放置在 src() 和 dest() 之间，并转换流（stream）中的文件
  - src() 也可以放在管道（pipeline）的中间，以根据给定的 glob 向流（stream）中添加文件
  - 新加入的文件只对后续的转换可用。如果 glob 匹配的文件与之前的有重复，仍然会再次添加文件

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(uglify())
    .pipe(dest('output/'));
}
```

### 分阶段输出

- dest() 可以用在管道（pipeline）中间用于将文件的中间状态写入文件系统
- 当接收到一个文件时，当前状态的文件将被写入文件系统，文件路径也将被修改以反映输出文件的新位置，然后该文件继续沿着管道（pipeline）传输

```js
const { src, dest } = require('gulp');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');
const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    .pipe(babel())
    .pipe(src('vendor/*.js'))
    .pipe(dest('output/'))
    .pipe(uglify())
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

### 模式

- src() 可以工作在三种模式下：缓冲（buffering）、流动（streaming）和空（empty）模式，这些模式可以通过对 src() 的 buffer 和 read 参数 进行设置
  - 缓冲（Buffering）模式
    - 默认模式，将文件内容加载内存中
    - 插件通常运行在缓冲（buffering）模式下，并且许多插件不支持流动模式
  - 流动（Streaming）模式
    - 主要用于操作无法放入内存中的大文件，例如巨幅图像或电影
    - 文件内容从文件系统中以小块的方式流式传输，而不是一次性全部加载
    - 如果需要流动模式，请查找支持此模式的插件或自己编写
  - 空（Empty）模式
    - 不包含任何内容，仅在处理文件元数据时有用

## Glob 详解

- glob 是由普通字符和/或通配字符组成的字符串，用于匹配文件路径
  - 可以利用一个或多个 glob 在文件系统中定位文件
- src() 方法接受一个 glob 字符串或由多个 glob 字符串组成的数组作为参数，用于确定哪些文件需要被操作
  - glob 或 glob 数组必须至少匹配到一个匹配项，否则 src() 将报错
    - 要抑制此错误，请将 allowEmpty 选项设置为 true
  - 当使用 glob 数组时，将按照每个 glob 在数组中的位置依次执行匹配 - 这尤其对于取反 glob 有用

### 字符串片段与分隔符

- 字符串片段（segment）是指两个分隔符之间的所有字符组成的字符串
- 在 glob 中，分隔符永远是 / 字符
  - 不区分操作系统，即便是在采用 \\ 作为分隔符的 Windows 操作系统中
  - 在 glob 中，\\ 字符被保留作为转义符使用
- 避免使用 Node 的 path 类方法来创建 glob，例如 path.join
  - 在 Windows 中，由于 Node 使用 \\ 作为路径分隔符，因此将会产生一个无效的 glob
- 避免使用 __dirname 和 __filename 全局变量
- 避免使用 process.cwd() 方法

### 特殊字符： * (一个星号)

- 在一个字符串片段中匹配任意数量的字符，包括零个匹配。适合匹配单级目录下的文件
  - '*.js' 这个 glob 能够匹配类似 index.js 的文件，但是不能匹配类似 scripts/index.js

### 特殊字符： ** (两个星号)

- 在多个字符串片段中匹配任意数量的字符，包括零个匹配。适合匹配嵌套目录下的文件
  - 请确保适当地限制带有两个星号的 glob 的使用，以避免匹配大量不必要的目录
  - 'scripts/**/*.js' 这个 glob 被适当地限制在 scripts/ 目录
    - 它将匹配类似 scripts/index.js、scripts/nested/index.js 和 scripts/nested/twice/index.js 的文件

### 特殊字符： ! (取反)

- 由于 glob 匹配时是按照每个 glob 在数组中的位置依次进行匹配操作的，所以 glob 数组中的取反 glob 必须跟在一个非取反的 glob 后面
  - 第一个 glob 匹配到一组匹配项，然后后面的取反 glob 删除这些匹配项中的一部分
- 如果取反 glob 只是由普通字符组成的字符串，则执行效率是最高的
  - ['script/**/*.js', '!scripts/vendor/']
- 如果任何非取反的 glob 跟随着一个取反 glob，任何匹配项都不会被删除
  - ['script/**/*.js', '!scripts/vendor/', 'scripts/vendor/react.js']
- 取反 glob 可以作为对带有两个星号的 glob 的限制手段
  - ['**/*.js', '!node_modules/']
  - 如果取反glob 是 !node_modules/**/*.js，那么各匹配项都必须与取反 glob 进行比较，这将导致执行速度极慢

### 匹配重叠

- 两个或多个 glob 故意或无意匹配了相同的文件就被认为是匹配重叠了
- 如果在同一个 src() 中使用了会产生匹配重叠的 glob，gulp 将尽力去除重叠部分，但是在多个 src() 调用时产生的匹配重叠是不会被去重的

## 使用插件

- Gulp 插件实质上是 Node 转换流，它封装了通过管道转换文件的常见功能，通常是使用 .pipe() 方法并放在 src() 和 dest() 之间
  - 可以更改经过流（stream）的每个文件的文件名、元数据或文件内容
- [gulp插件搜索页](https://www.gulpjs.com.cn/docs/getting-started/using-plugins/)

```js
const { src, dest } = require('gulp');
const uglify = require('gulp-uglify');
const rename = require('gulp-rename');

exports.default = function() {
  return src('src/*.js')
    // gulp-uglify 插件并不改变文件名
    .pipe(uglify())
    // 因此使用 gulp-rename 插件修改文件的扩展名
    .pipe(rename({ extname: '.min.js' }))
    .pipe(dest('output/'));
}
```

### 是否使用插件

- 许多操作都应当通过使用独立的功能模块或库来实现

```js
const { rollup } = require('rollup');

// Rollup 提供了基于 promise 的 API，在 `async` 任务中工作的很好
exports.default = async function() {
  const bundle = await rollup.rollup({
    input: 'src/index.js'
  });

  return bundle.write({
    file: 'output/bundle.js',
    format: 'iife'
  });
}
```

- 插件应当总是用来转换文件的。其他操作都应该使用（非插件的） Node 模块或库来实现

```js
const del = require('delete');

exports.default = function(cb) {
  // 直接使用 `delete` 模块，避免使用 gulp-rimraf 插件
  del(['output/*.js'], cb);
}
```

### 条件插件

- 因为插件的操作不应该针对特定文件类型，因此你可能需要使用像 gulp-if 之类的插件来完成转换某些文件的操作

```js
const { src, dest } = require('gulp');
const gulpif = require('gulp-if');
const uglify = require('gulp-uglify');

function isJavaScript(file) {
  // 判断文件的扩展名是否是 '.js'
  return file.extname === '.js';
}

exports.default = function() {
  // 在同一个管道（pipeline）上处理 JavaScript 和 CSS 文件
  return src(['src/*.js', 'src/*.css'])
    // 只对 JavaScript 文件应用 gulp-uglify 插件
    .pipe(gulpif(isJavaScript, uglify()))
    .pipe(dest('output/'));
}
```

### 内联插件 ???

- 内联插件是一次性的转换流
- 可以通过在 gulpfile 文件直接书写需要的功能
- 两种应用场景
  - 避免自己创建并维护插件
  - 避免 fork 一个已经存在的插件并添加自己所需的功能

```js
const { src, dest } = require('gulp');
const uglify = require('uglify-js');
const through2 = require('through2');

exports.default = function() {
  return src('src/*.js')
    // 创建一个内联插件，从而避免使用 gulp-uglify 插件
    .pipe(through2.obj(function(file, _, cb) {
      if (file.isBuffer()) {
        const code = uglify.minify(file.contents.toString())
        file.contents = Buffer.from(code)
      }
      cb(null, file);
    }))
    .pipe(dest('output/'));
}
```

## 文件监控

- gulp api 中的 watch() 方法利用文件系统的监控程序将 globs 与任务进行关联
  - 它对匹配 glob 的文件进行监控，如果有文件被修改了就执行关联的任务
  - 如果被执行的任务没有触发 异步完成 信号，它将永远不会再次运行了
  - 此 API 的默认设置是基于通常的使用场景的，而且提供了内置的延迟和排队机制

```js
const { watch, series } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

// 可以只关联一个任务
watch('src/*.css', css);
// 或者关联一个任务组合
watch('src/*.js', series(clean, javascript));
```

### 避免同步任务

- 与文件监控程序关联的任务不能是同步任务
- 如果你将同步任务关联到监控程序，则无法确定任务的完成情况，任务将不会再次运行（假定当前正在运行）
  - 由于文件监控程序会让你的 Node 进程保持持续运行，因此不会有错误或警告产生
  - 由于进程没有退出，因此无法确定任务是否已经完成还是运行了很久很久了

### 可监控的事件

- 默认情况下，只要创建、更改或删除文件，文件监控程序就会执行关联的任务
- 可以在调用 watch() 方法时通过 events 参数进行指定
  - 可用的事件有 'add'、'addDir'、'change'、'unlink'、'unlinkDir'、'ready'、'error'
  - 还有一个 'all' 事件，它表示除 'ready' 和 'error' 之外的所有事件

```js
const { watch } = require('gulp');

// 所有事件都将被监控
watch('src/*.js', { events: 'all' }, function(cb) {
  // body omitted
  cb();
});
```

### 初次执行

- 调用 watch() 之后，关联的任务是不会被立即执行的，而是要等到第一次文件修之后才执行
- ignoreInitial: false 可以实现调用 watch() 之后立即执行

```js
const { watch } = require('gulp');

// 关联的任务（task）将在启动时执行
watch('src/*.js', { ignoreInitial: false }, function(cb) {
  // body omitted
  cb();
});
```

### 队列

- watch() 方法能够保证当前执行的任务不会再次并发执行
- 当文件监控程序关联的任务正在运行时又有文件被修改了，那么所关联任务的这次新的执行将被放到执行队列中等待，直到上一次关联任务执行完之后才能运行
- 每一次文件修改只产生一次关联任务的执行并放入队列中
- queue: false 可以禁止队列

```js
const { watch } = require('gulp');

// 每次文件修改之后关联任务都将执行（有可能并发执行）
watch('src/*.js', { queue: false }, function(cb) {
  // body omitted
  cb();
});
```

### 延迟

- 文件更改之后，只有经过 200 毫秒的延迟之后，文件监控程序所关联的任务才会被执行
  - 为了避免在同使更改许多文件时（例如查找和替换操作）过早启动任务的执行
- delay: 500 调整延迟时间为500毫秒

```js
const { watch } = require('gulp');

// 文件第一次修改之后要等待 500 毫秒才执行关联的任务
watch('src/*.js', { delay: 500 }, function(cb) {
  // body omitted
  cb();
});
```

### 使用监控程序实例

- 基本不使用
- 如果你需要对被修改的文件进行完全的掌控 （例如访问文件路径或元数据）请使用从 watch() 返回的 [chokidar](https://www.npmjs.com/package/chokidar) 实例
- 返回的 chokidar 实例没有队列、延迟和异步完成（async completion）这些功能

### 可选的依赖项

- Gulp 有一个名为 fsevents 的可选依赖项，他是一个特定于 Mac 系统的文件监控程序
- 安装 fsevents 时出现的警告信息 - "npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents" - 这并不是什么问题，忽略即可
  - 如果跳过 fsevents 的安装，将使用一个备用文件监控程序，后续在 gulpfile 中产生的任何错误都将与此警告无关

## 文件系统模式

- 文件系统模式决定文件的权限
- 文件系统上的大多数文件和目录将具有相当宽松的模式，允许 gulp 代表您读取/写入/更新文件
- 默认情况下，gulp 将创建与运行进程具有相同权限的文件，但是您可以通过 src()、 dest() 中的选项配置模式
- 遇到权限(EPERM)问题，请检查文件上的模式

## 模块

- Gulp 由许多小模块组成，这些模块被拉到一起以实现内聚性工作
- 通过在小模块中使用 semver，作者可以在不发布 gulp 新版本的情况下发布 bug 修复和特性
- 通常，当没有看到主存储库上的进展时，工作是在其中一个模块中完成的
- 如果遇到问题，可以尝试使用 npm update 命令更新当前模块，如果 无效可以提issue