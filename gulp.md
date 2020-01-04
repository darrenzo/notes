# gulp

## 安装

- 如果之前安装过**gulp**，可以执行 `npm rm --global gulp` 来移除旧的安装
- 通过 `npm install --global gulp-cli` 安装新的 CLI， `gulp -v` 检查**gulp**版本
- 在项目文件夹下 `npm init` 初始化后，`npm install --save-dev gulp` 安装**gulp**依赖

## gulpfile.js

- **gulpfile.js**文件是编写具体gulp任务的地方
- **gulpfile.js**文件可以用预编译语言，例如 **TypeScript** 和 **Babel**
  - 如果用 TypeScript，则需要把 **gulpfile.js** 改名为 **gulpfile.ts**，并安装 **ts-node** 模块
  - 如果用 Babel，则需要把 **gulpfile.js** 改名为 **gulpfile.babel.js**，并安装 **@babel/register** 模块
- Node的模块解析允许使用名为gulpfile.js的目录替换gulpfile.js文件，该目录包含一个index.js文件，该文件被视为gulpfile.js。然后，该目录可以包含用于任务的各个模块。如果使用别的语言编写，请相应地命名文件夹和文件

<!-- TODO 待续 -->
## tasks
