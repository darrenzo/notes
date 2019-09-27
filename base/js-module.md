# Module

## CommonJS

- 最开始的目的是让浏览器之外的 JavaScript （比如服务器端或者桌面端）能够通过模块化的方式来开发和协作
- 在 CommonJS 的规范中，每个 JavaScript 文件就是一个独立的模块上下文（module context），在这个上下文中默认创建的属性都是私有的，对其他文件是不可见的
  - 如果想在多个文件分享变量，必须定义为global对象的属性。（不推荐）
- CommonJS 规范的主要适用场景是服务器端编程，所以采用同步加载模块的策略。如果我们依赖3个模块，代码会一个一个依次加载它们
- 该模块实现方案主要包含 **require** 与 **module** 这两个关键字，其允许某个模块对外暴露部分接口并且由其他模块导入使用

```js
//sayModule.js
function SayModule () {
    this.hello = function () {
        console.log('hello');
    };

    this.goodbye = function () {
        console.log('goodbye');
    };
}

module.exports = SayModule;

//main.js 引入sayModule.js
var Say = require('./sayModule.js');
var sayer = new Say();
sayer.hello(); //hello
```

- 入口文件
  - 一般都会有一个主文件（入口文件），在index.html中加载这个入口文件，然后在这个入口文件中加载其他文件
  - 可以通过在package.json中配置main字段来指定入口文件。

### Node.js 模块处理

- 作为一个服务器端的解决方案，CommonJS 需要一个兼容的脚本加载器作为前提条件。该脚本加载器必须支持名为 require 和 module.exports 的函数，它们将模块相互导入导出
- Node有自己的模块化实现，由于Node 在服务端的流行，Node 的模块形式被（不正确地）称为 CommonJS
- Node 两大类模块
  - 核心模块
    - Node.js标准的API中提供的模块，如fs、http、net等
    - 由Node.js官方提供，编译成了二进制代码，可以直接通过require获取核心模块，例如require('fs')
    - 核心模块拥有最高的加载优先级，如果有模块与核心模块命名冲突，Node.js总是会加载核心模块
  - 文件模块
    - 是存储为单独的文件（或文件夹）的模块，主要为三类
      - **.js**：通过fs模块同步读取js文件并编译执行，通过**module._compile**方法进行加载
      - **.json**：读取文件，调用**JSON.parse**解析加载
      - **.node**：通过C/C++进行编写的Addon。通过**process.dlopen**方法进行加载
    - 在不显式指定文件模块扩展名的时候，Node.js会分别试图加上 **.js**、**.json**、**.node**，并按顺序依次查找

#### 由 require() 参数决定加载方式

- 参数以"/"开头：以绝对路径的方式查找模块名称
- 参数以"./"、"../"开头：以相对路径的方式来查找模块
- 参数不以"/"、"./"、"../"开头
  - 如果是核心模块，则从原生模块中查找
  - 如果不是核心模块，则通过查找 **node_modules** 加载模块（使用npm获取的包通常就是以这种方式加载的）

#### Node的加载缓存机制

- Node.js模块不会被重复加载
  - Node.js通过文件名缓存所有加载过的文件模块，所以以后再访问到时就不会重新加载了
  - Node.js是根据实际文件名缓存的，而不是**require()**提供的参数缓存的（例如即使分别通过require('express')和require('./node_modules/express')加载两次，也不会重复加载，因为尽管两次参数不同，解析到的文件却是同一个）
  - Node.js 中的模块在加载之后是以单例化运行，并且遵循值传递原则：如果是一个对象，就相当于这个对象的引用

#### Node 模块载入过程

- 加载文件模块的工作，主要由原生模块**module**来实现和完成
- 原生模块**module**在启动时已经被加载，进程直接调用到**runMain**静态方法

```js
// 例如：命令行执行 node app.js 时 node 内部执行原理

Module.runMain = function () {
    // require方法实际调用的就是module._load方法
    Module._load(process.argv[1], null, true);
};

//_load静态方法在分析文件名之后执行
var module = new Module(id, parent);

//并根据文件路径缓存当前模块对象，该模块实例对象则根据文件名加载
module.load(filename);
```

- **.js** 的编译过程
  - 模块载入机制均定义在**lib/module.js**中
  - Node.js 在编译js文件的过程中会对js文件内容进行头尾包装

  ```js
  //circle.js
  var PI = Math.PI;
  exports.area = function (r) {
      return PI * r * r;
  };
  exports.circumference = function (r) {
      return 2 * PI * r;
  };

  //app.js
  var circle = require('./circle.js');
  console.log( 'The area of a circle of radius 4 is ' + circle.area(4));

  //app包装后
  // 因此写模块时才可以直接使用require、module等变量
  // __filename和__dirname在查找文件路径的过程中分析得到后传入
  // module变量是所引入的模块对象自身，exports是在module的构造函数中初始化的一个空对象（{}，而不是null）
  // load方法在载入、编译、缓存了module后，返回module的exports对象, 所以circle.js文件中只有定义在exports对象上的方法才能被外部调用
  (function (exports, require, module, __filename, __dirname) {
      var circle = require('./circle.js');
      console.log('The area of a circle of radius 4 is ' + circle.area(4));
  });

  // 这段代码会通过**vm**原生模块的**runInThisContext**方法执行（类似eval，只是具有明确上下文，不污染全局），返回为一个具体的function对象。最后传入module对象的exports，require方法，module，文件名，目录名作为实参并执行。
  ```

#### require() 函数

- require 引入的对象主要是函数
- 当 Node 调用 require() 函数，并且传递一个文件路径 给它的时候，Node 会经历如下几个步骤
  - Resolving：找到文件的绝对路径
  - Loading：判断文件内容类型
  - Wrapping：打包，给这个文件赋予一个私有作用范围。这是使 require 和 module 模块在本地引用的一种方法
  - Evaluating：VM 对加载的代码进行处理的地方
  - Caching：当再次需要用这个文件的时候，不需要重复一遍上面步骤
- 文件查找策略( 文件模块缓存 > 原生模块缓存 > 原生模块 > 文件模块 )
  1. 从文件模块缓存中加载
     - 尽管原生模块与文件模块的优先级不同，但是优先级最高的是从文件模块的缓存中加载已经存在的模块
       - 在缓存区里文件模块比原生模块优先级高的原因，个人猜测是考虑到这时使用文件模块的可能性更高
  2. 从原生模块加载
     - 原生模块的优先级仅次于文件模块缓存的优先级
     - require方法在解析文件名之后，优先检查模块是否在原生模块列表中
     - 原生模块也有一个缓存区，同样也是优先从缓存区加载。如果缓存区没有被加载过，则调用原生模块的加载方式进行加载和执行
  3. 从文件加载
     - 当文件模块缓存中不存在，而且不是原生模块的时候，Node.js会解析require方法传入的参数，并从文件系统中加载实际的文件
     - 加载过程中的包装和编译细节就是调用load方法
- require(X) 完整查找顺序
  1. 如果 X 是内置模块（比如 require('http')）
     - 返回该模块
     - 不再继续执行
  2. 如果 X 以 "./" 或者 "/" 或者 "../" 开头
     - 根据 X 所在的父模块，确定 X 的绝对路径
     - 将 X 当成文件，依次查找下面文件，只要其中有一个存在，就返回该文件，不再继续执行
        1. X
        2. X.js
        3. X.json
        4. X.node
  3. 将 X 当成目录，依次查找下面文件，只要其中有一个存在，就返回该文件，不再继续执行
        1. X/package.json（main字段）
        2. X/index.js
        3. X/index.json
        4. X/index.node
  4. 如果 X 不带路径
     - 根据 X 所在的父模块，确定 X 可能的安装目录
     - 依次在每个目录中，将 X 当成文件名或目录名加载
  5. 抛出 "not found"

#### Node 模块循环依赖

```js
//创建两个文件，module1.js 和 module2.js，并且让它们相互引用

// module1.js
exports.a = 1;
require('./module2');
exports.b = 2;
exports.c = 3;

// module2.js
const Module1 = require('./module1');
console.log('Module1 is partially loaded here', Module1);

// module2 从 module1 的 exports 对象中能得到的就是在发生循环依赖之前的部分，即只有 a 属性被引入，因为 b 和 c 都需要在引入 module2 之后才能加载进来
```

## AMD 规范

- AMD 是 **Asynchronous Module Definition** 的简称，即 **异步模块定义**，是从 CommonJS 讨论中诞生的
- AMD 优先照顾浏览器的模块加载场景，使用了异步加载和回调的方式
- AMD模式可以用于浏览器环境并且允许非同步加载模块，也可以按需动态加载模块
- AMD 一样需要脚本加载器（RequireJS）
- AMD 只需要对 **define** 方法的支持
- **define** 方法需要三个参数（只有函数参数是必须的，即独立的模块）
  - 模块名称
  - 模块运行的依赖数组
  - 所有依赖都可用之后执行的函数（该函数按照依赖声明的顺序，接收依赖作为参数）
- **define** 既是一种引用模块的方式，也是定义模块的方式

```js
// lib/sayModule.js
define(function (){
    return {
        sayHello: function () {
            console.log('say');
        }
    };
});

// lib/talkModule.js
define(function (){
    return {
        sayHello: function () {
            console.log('talk');
        }
    };
});

// main.js
// 依赖并发加载, 回调延迟执行
// 执行该模块代码时，也就是执行 define 函数的第二个参数中定义的函数功能
define(['./lib/sayModule', './lib/talkModule'], function (say, talk){
    say.sayHello(); // say
    talk.sayHello(); // talk
})
```

### RequireJS

- 前端的模块化管理的工具库，遵循AMD规范
- 通过一个函数来将所有所依赖的模块实现装载进来，然后返回一个新的函数（模块）
- 使用
  - 在主页面index.html中先通过script标签引入require.min.js
  - 再通过script标签引入一个入口文件main.js，此入口文件一般用于配置（require.config），以及引入其他模块
- defined用于定义模块，RequireJS要求每个模块均放在独立的文件之中
- 按照是否有依赖其他模块的情况分为独立模块和非独立模块
  - 独立模块: 不依赖其他模块, 直接定义

  ```js
  define({
      methodOne: function (){},
      methodTwo: function (){}
  });

  //等价于

  define(function (){
      return {
          methodOne: function (){},
          methodTwo: function (){}
      };
  });
  ```

  - 非独立模块: 对其他模块有依赖

  ```js
  define([ 'moduleOne', 'moduleTwo' ], function(mOne, mTwo){
      ...
  });

  //或者

  define( function( require ){
      // require 用在 define 方法内部
      var mOne = require( 'moduleOne' ),
          mTwo = require( 'moduleTwo' );
      ...
  });

  // 或者

  require(['foo','bar'],function(foo,bar){
        // ……
  })

  // require 允许传入第三个参数
  require(
      [ "backbone" ],
      function ( Backbone ) {
          return Backbone.View.extend({ /* ... */ });
      },
      function (err) {
          // ...
      }
  );
  ```

  - 配置
    - require方法本身也是一个对象，它带有一个config方法，用来配置require.js运行参数

    ```js
    require.config({
        paths: {
            jquery:'lib/jquery'
        }
    });
    // 此时就可以直接在文件中 require(['jquery'],function($){})
    ```

    - paths：paths参数指定各个模块的位置
      - 这个位置可以是同一个服务器上的相对位置，也可以是外部网址
      - 可以为每个模块定义多个位置，如果第一个位置加载失败，则加载第二个位置
    - shim：有些库不是AMD兼容的，这时就需要指定shim属性的值
      - shim可以理解成“垫片”，用来帮助require.js**加载非AMD规范的库**

    ```js
    require.config({
        paths: {
            "backbone": "vendor/backbone",
            "underscore": "vendor/underscore"
        },
        shim: {
            "backbone": {
                deps: [ "underscore" ],
                exports: "Backbone"
            },
            "underscore": {
                exports: "_"
            }
        }
    });
    ```

## UMD 规范

- 统一模块定义（**Universal Module Definition**）就是将 **AMD** 和 **CommonJS** 合在一起的一种尝试
- 常见的做法是将 **CommonJS** 语法包裹在兼容 **AMD** 的代码中
- 该模式的核心思想在于所谓的 IIFE（**Immediately Invoked Function Expression**），该函数会根据环境来判断需要的参数类别

```js
(function(define) {
    define(function () {
        return {
            sayHello: function () {
                console.log('hello');
            }
        };
    });
}(
    typeof module === 'object' && module.exports && typeof define !== 'function' ?
    function (factory) { module.exports = factory(); } :
    define
));
```

## CMD 规范

- CMD 全称为 **Common Module Definition**
- 在CMD中，一个模块就是一个文件
- 全局函数**define**，用来定义模块
- 参数 factory 可以是一个函数，也可以为对象或者字符串, 当 factory 为对象、字符串时，表示模块的接口就是该对象、字符串

```js
// 定义JSON数据模块
define({ "foo": "bar" });

// factory 为函数的时候，表示模块的构造方法，执行构造方法便可以得到模块向外提供的接口
define( function(require, exports, module) {
    // 模块代码
});
```

### SeaJS

- 遵循CMD规范，与NodeJS般的书写模块代码
- 依赖自动加载，配置清晰简洁
- **seajs.use()** 用来在页面中加载一个或者多个模块

```js
// 加载一个模块
seajs.use('./a');

// 加载模块，加载完成时执行回调
seajs.use('./a'，function(a){
    a.doSomething();
});

// 加载多个模块执行回调
seajs.use(['./a','./b']，function(a , b){
    a.doSomething();
    b.doSomething();
});
```

## ES6 module

- ES6 的模块自动采用严格模式，不管有没有在模块头部加上"use strict"

### 导出 Export

- 声明的变量，对外都是只读的。但是导出的是对象类型的值，就可修改

```js
// profile.js
export var firstName = 'qiqi';
export var lastName = 'haobenben';

// 等价于

var firstName = 'qiqi';
var lastName = 'haobenben';
export {firstName, lastName}
```

- 导出重命名
  - export输出的变量就是本来的名字，但是可以使用as关键字重命名

```js
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};

// 上面代码使用as关键字，重命名了函数v1和v2的对外接口。重命名后，v2可以用不同的名字输出两次
```

- export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系

```js
// 报错
export 1;

// 报错
var m = 1;
export m;
// 没有提供对外的接口
// 第一种写法直接输出1，第二种写法通过变量m，还是直接输出1。1只是一个值，不是接口

// 正确的姿势
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
// 它们的实质是，在接口名与模块内部变量之间，建立了一一对应的关系
```

- export命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错, import 同理
- 默认导出（export default）
  - 每个模块支持我们导出一个没有名字的变量，使用关键语句**export default**来实现
  - 一个模块只能有一个默认输出

  ```js
  // 默认输出
  export default function diff() { // 输出
    // ...
  }
  // 导入默认输出的可以直接取任意名字
  import diff from 'diff'; // 输入
  
  // 正常输出
  export function diff() { // 输出
    // ...
  };
  // 导入正常输出的需要 {}
  import {diff} from 'diff';
  ```

  - 因为**export default**本质是将该命令后面的值，赋给default变量以后再输出，所以可以直接将一个值写在**export default**之后

  ```js
  export default 42
  ```

### 导入import

- import命令接受一对大括号，里面指定要从其他模块导入的变量名
- 大括号里面的变量名，必须与被导入模块对外接口的名称相同
- 如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名
- 导入不存在的变量，值为undefined

```js
import { lastName as surename } from './profile';
```

- import后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径，**.js**路径可以省略
- 如果只是模块名，不带有路径，那么必须有配置文件，告诉 JavaScript 引擎该模块的位置
- import命令具有提升效果，会提升到整个模块的头部，首先执行

```js
foo();
import { foo } from 'my_module';
// 不会报错，因为 import的执行早于foo的调用
```

- 由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构

```js
// 以下三种均错误

import { 'f' + 'oo' } from 'my_module';

let module = 'my_module';
import { foo } from module;

if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

- import语句会执行所加载的模块

```js
import 'lodash';
//上面代码仅仅执行lodash模块，但是不输入任何值
```

- 在一条import语句中，同时输入默认方法和其他变量

```js
import init, { each } from 'lodash';
```

### export 与 import 的复合写法

- 如果在一个模块之中，先输入后再输出同一个模块，import语句可以与export语句写在一起

```js
export { foo, bar } from 'my_module';
// 等价于
import { foo, bar } from 'my_module';
export { foo, bar };

// 接口改名
export { foo as myFoo } from 'my_module';

// 整体输出
export * from 'my_module';
```

### ES6 中的循环引用

- ES6 中，imports 是 exprts 的只读视图，即 imports 都指向 exports 原本的数据

```js
//------ lib.js ------
export let counter = 3;
export function incCounter() {
    counter++;
}

//------ main.js ------
import { counter, incCounter } from './lib';

// The imported value `counter` is live
console.log(counter); // 3
incCounter();
console.log(counter); // 4

// The imported value can’t be changed
counter++; // TypeError
```

```js
//------ a.js ------
import {bar} from 'b'; // (1)
export function foo() {
  bar(); // (2)
}

//------ b.js ------
import {foo} from 'a'; // (3)
export function bar() {
  if (Math.random()) {
    foo(); // (4)
  }
}
// 假设先加载模块 a，在模块 a 加载完成之后，bar 间接性地指向的是模块 b 中的 bar。无论是加载完成的 imports 还是未完成的 imports，imports 和 exports 之间都有一个间接的联系，所以总是可以正常工作
```

## commonJS规范 与 AMD规范的区别

- CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作
  - 由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用
- AMD规范则是异步加载模块，允许指定回调函数，在回调函数中执行操作
  - 浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范
  
- AMD规范允许输出的模块兼容CommonJS规范，这时define方法需要写成下面这样

```js
define(function(require,exports,module){
    var someModule = require("someModule");
    var anotherModule = require("anotherModule");
    // ……
    exports.asplode = function(){

    }
})
```

## AMD 和 CMD 的区别

- 最大的区别是对依赖模块的执行时机处理不同 （注意不是加载的时机或者方式不同）
  - AMD在加载模块完成后就会执行该模块，所有模块都加载（并发加载）执行完后再进入回调函数，执行主逻辑。这样的效果就是依赖模块的执行顺序和书写顺序不一定一致
  - CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的
- 两者加载模块都是异步的
  - AMD依赖前置，js可以方便知道依赖模块是谁，立即加载
  - CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了哪些模块 ？？？？
    - 这也是很多人诟病CMD的一点，牺牲性能带来开发的便利性，实际上解析模块用的时间短到可以忽略

## commonJS模块 与 ES6模块的区别

### commonJS

- 对于基本数据类型，属于复制
  - 即会被模块缓存。同时，在另一个模块可以对该模块输出的变量重新赋值
- 对于复杂数据类型，属于浅拷贝。
  - 由于两个模块引用的对象指向同一个内存空间，因此对该模块的值做修改时会影响另一个模块
- 当使用require命令加载某个模块时，就会运行整个模块的代码
- 当使用require命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值
  - CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存
- 循环加载时，属于加载时执行
  - 即脚本代码在require的时候，就会全部执行
  - 一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出

### ES6模块

- ES6模块中的值属于**动态只读引用**
  - 对于只读来说，即不允许修改引入变量的值
    - import的变量是只读的，不论是基本数据类型还是复杂数据类型
    - 当模块遇到import命令时，就会生成一个只读引用
    - 等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值
  - 对于动态来说，原始值发生变化，import加载的值也会发生变化。不论是基本数据类型还是复杂数据类型
- 循环加载时，ES6模块是动态引用
  - 只要两个模块之间存在某个引用，代码就能够执行