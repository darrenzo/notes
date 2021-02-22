# vue

## 安装

- npm install webpack -g
- npm install vue-cli -g
  - 安装最新版的脚手架前需要先卸载旧的脚手架，再用 npm install @vue/cli -g
- 脚手架自动生成：不能用 bash
  - vue create hello-world
- 不使用脚手架
  - 新建文件夹vue-test
  - vue init webpack-simple 工程名字<工程名字不能用中文> 一路默认 再cd进工程根目录
  - npm install 一定要npm下载
  - npm install vue-router vue-resource --save
  - npm run dev生成一系列文件 （当网页成功显示时，会占用8080端口，但是关掉网页，再run dev时，原端口依旧被占用，cmd会报错）
    - CMD关闭占用端口的程序：输入 netstat -ano|findstr 8080 ，找到占用进程的pid. 输入命令taskkill /pid 4708 /f，结束该进程。<!-- 假如 4708是查询到的pid -->

## chrome调试

- 在chrome浏览器调试时，安装Vue Devtool扩展工具，并且将`Vue.config.devtools`设置为true时，可以在浏览器中看到实例中的所有状态

## fastclick插件与某些插件冲突时，第三方插件阻止了默认的点击事件，自己代理点击事件，会被fastclick阻止，此时只需要在需要点击事件的元素上加上class="needclick"

## 插件Vue-Lazyload使用： 在img标签上加上v-lazy="img.src"

## vue 渲染

- 在vue项目中，如果将模版语法写在html文件中，那么页面在vue加载完成并渲染完成之前，会短暂地将源代码暴露在页面上，影响了用户体验，此时可以在根节点上增加`v-cloak`，并在css中写上`#app[v-cloak] {diaplay: none;}`，vue在渲染完成时，会将`v-cloak`去掉

## css隔离

- style标签的scoped属性只能保证本组件的样式不会影响其他组件，不能保证其他组件的样式不影响到本组件
- 实质上增加scoped属性之后，当前组件的所有DOM节点都会增加一个`data-v-${hash}`做标识，同样生成的css中将只应用此标识的节点`.selector[data-v-${hash}]`，这样就能实现css样式值应用于本组件
- 当样式只需应用到当前组件的子组件时，如果直接用`.fatherSelector .childSelector`，那么生成的css中将是`.fatherSelector .childSelector[data-v-${fatherHash}]`，这样将不会应用到子组件的节点，此时应该用深度作用选择器`.fatherSelector >>> .childSelector`，此时生成的css将是`.fatherSelector[data-v-${fatherHash}] .childSelector`
  - 使用了 **sass** 或 **less** 预处理器时，无法正确解析深度选择器，需要使用 **/deep/** 或 **::v-deep** 操作符，它们都是 **>>>** 的别名

  ```scss
  .select {
    width: 100px;

    /deep/ .el-input__inner {
      border: 0;
      color: #000;
    }
  }
  ```

  - 通过 v-html 创建的 DOM 内容不受 scoped 样式影响（动态生成的html不会加上hash属性），但是你仍然可以通过深度作用选择器来为他们设置样式
  - 使用scoped时性能下降（慢很多倍）

- 一般在使用到less或者sass的项目中，可以用 **style-resources-loader** 插件（详见**vue-cli.md**中的自动化导入）导入全局文件到项目中，例如，需要编译不同主题的时候，可以导入一个主题变量文件，在组件中直接使用这些变量，而不需要每个组件都`@import`进来
- 可以在一个组件中同时使用有 scoped 和非 scoped 样式

```html
<style>
/* 全局样式 */
</style>

<style scoped>
/* 本地样式 */
</style>
```

## v-bind 双向绑定

- [原理](https://github.com/bison1994/two-way-data-binding/blob/master/index.html#L46)
- 双大括号会将数据解释为普通文本，而非 HTML 代码
- 当这些数据改变时，视图会进行重渲染。值得注意的是只有当实例被创建时 data 中存在的属性是响应式的。也就是说如果你添加一个新的属性app.b=2,那么对 b 的改动将不会触发任何视图的更新。如果你知道你会在晚些时候需要一个属性，但是一开始它为空或不存在，那么你仅需要设置一些初始值
- 除了 data 属性，Vue 实例暴露了一些有用的实例属性与方法。它们都有前缀 $，以便与用户定义的属性区分开来
- 类似于 :value="arr[index]"写法时，注意数组初始化是空数组，需要异步获取数组数据时，需要在对应元素上加上v-if判断或者v-for遍历，否则数组初始化为空时取值会报错undefined

```html
    <template>
        <div id="app">
            <span v-bind:title="title">
            <!-- 简写 :title -->
                {{message}}  
            </span>
        </div>
    </template>

    <script>
        export default {
            el: '#app',
            data() {   // app.$data获取该data对象 其他属性也可以用$进行相似操作 data最好写成函数形式，确保每个data的属性都是返回的唯一值
                return {
                    message: 'Hello Vue!'  
                }
            }
        }
    </script>
```

## class 样式绑定

```html
    <template>
        <div id="app">
            <!-- 当 isActive 或者 hasError 变化时，class 列表将相应地更新 -->
            <div class="static" :class="{ active: isActive, 'text-danger': hasError }"></div>

            <!-- 对象语法 -->
            <div class="static" :class="classObj"></div>

            <!-- 对象语法结合计算属性 -->
            <div class="static" :class="classObj2"></div>

            <!-- 数组语法：  字符串， 变量， 对象， 三元运算式-->
            <div :class="['static', errorClass, { active: isActive }, isActive ? activeClass : '']"></div>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        classObj: {
                            active: true,
                            'text-danger': false
                        }，
                        isActive: true,
                        error: null,
                        activeClass: 'active',
                        errorClass: 'text-danger'
                    }
                }，
                computed: {
                    classObj2: function () {
                        return {
                            active: this.isActive && !this.error,
                            'text-danger': this.error && this.error.type === 'fatal'
                        }
                    }
                }
            }
    </script>
```

- 当你在一个自定义组件上用到 class 属性的时候，这些类将被添加到根元素上面，这个元素上已经存在的类不会被覆盖。

```html
    <template>
        <my-component class="baz boo"></my-component>
    </template>
    <script>
        Vue.component('my-component', {
            template: '<p class="foo bar">Hi</p>'
        })
    </script>
    <!-- 结果：<p class="foo bar baz boo">Hi</p>  同样适用于绑定class的其他语法 -->
```

- 另一种写法（用在小型项目里）
- 当使用全局定义组件的时候，注意promise的then和catch的回调函数里，this会指向window，需要使用箭头函数解决
- 注意，此时子组件emit一个事件的名字不能用驼峰和连字符写法，有可能无效

```html
    <html>
        <head>
            <meta charset="utf-8">
            <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
        </head>
        <body>
            <div id="app">
                <!-- 如果是要遍历生成，需要包一层template标签 -->
                <template v-for="(plan, index) in plans">
                    <my-component></my-component>
                </template>

                <my-component></my-component>

                <script type="text/x-template" id="my-component">
                    <div>
                        <p>This is the content of component</p>
                        <p>Hello Vue!</p>
                    </div>
                </script>
            </div>
            <script>
                Vue.component('my-component',{
                        template:'#my-component'
                });
                var app=new Vue({
                    el:"#app"
                });
            </script>
        </body>
    </html>
```

- 浏览器兼容写法

```html
    <!-- 这会渲染数组中最后一个被浏览器支持的值。在这个例子中，如果浏览器支持不带浏览器前缀的 flexbox，那么渲染结果会是 display: flex。 -->
    <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

## v-model 表单输入双向绑定value值

- v-model绑定的是表单的value值
- v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值。因为它会选择 Vue 实例数据来作为具体的值。你应该通过 JavaScript 在组件的 data 选项中声明初始值。
- 对于要求 IME (如中文、日语、韩语等) (IME 意为“输入法”)的语言，你会发现 v-model 不会在 ime 输入中得到更新。如果你也想实现更新，请使用 input 事件。
- 在v-for 中，v-model不能直接绑定 (item, index) in arr 中的item， 得使用arr[index] （遍历对象时同理）
  - item是别名，相当于函数体内的局部变量，通过v-model向局部变量写入值，无法触发外部的实际数据的改变

```html
    <template>
        <div id="app">
            <p>{{ message }}</p>
            <input v-model="message">
            <!-- 相当于 <input :value="message" @input="message = $event.target.value" /> -->
        </div>
    </template>
    <script>
        export default {
            el: '#app',
            data() {
                return {
                    message: 'Hello Vue!'
                }
            }
        }
    </script>
```

- 如果v-model绑定的是个计算属性，则双向绑定失效，需要通过设置get和set方法实现

```js
computed: {
    planDiscount: {
        get() {
            return this.planData.discountRate * 100
        },
        set(value) {
            this.planData.discountRate = value / 100;
        }
    }
}
```

## 表单输入绑定

### 单选框

```html
    <input id="man" type="radio" name="gender" value="man" v-model="gender"/>
    <label for="man">男</label>
    <input id="women" type="radio" name="gender" value="woman" v-model="gender"/>
    <label for="women">女</label>
```

### 单个勾选框（逻辑值）

```html
    <!-- 显示为勾选true,取消勾选为false -->
    <input id="checkbox" type="checkbox" v-model="checked">
    <label for="checkbox">{{ checked }}</label>
```

### 多个勾选框（绑定到同一数组）

```html
    <template>
        <div id="app">
            <input id="jack" type="checkbox" value="Jack" v-model="checkedNames">
            <label for="jack">Jack</label>
            <input id="john" type="checkbox" value="John" v-model="checkedNames">
            <label for="john">John</label>
            <br>
            <span>Checked names: {{ checkedNames }}</span>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        checkedNames: [] // Checked names: []为初始值，每选中一个都在括号中填入其value
                    }
                }
            }
    </script>
```

### select 单选列表

- 如果第一个选项是不需要匹配任意选项，就加上disabled

```html
    <template>
        <div id="app">
            <select v-model="selected">
                <option disabled value="">请选择</option>
                <option value="a">1</option>
                <option value="b">2</option>
            </select>
            <span>Selected: {{ selected }}</span>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        selected: ''
                    }
                }
            }
    </script>
```

### select 多选列表

- 给select标签加上multiple属性
- data里绑定的属性应该是个数组 data() { return { selected: [] } }
- 数据要绑定到一个数组,即使写引号，选中数据依旧更改为数组再填入数据

### select应用，动态选项

- v-model作为一个中介，把option的value值和data中的selected值双向绑定，selected值又和{{selected}}绑定，{{}}方法只能和data中的数据双向绑定，而唯一能随option.value变化的只有selected

```html
    <template>
        <div id="app">
            <select v-model="selected">
                <option v-for="option in options" :value="option.value">
                    {{ option.text }}
                </option>
            </select>
            <span>Selected: {{ selected }}</span>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        selected: 'A',
                        options: [
                            { text: 'One', value: 'A' },
                            { text: 'Two', value: 'B' },
                            { text: 'Three', value: 'C' }
                        ]
                    }
                }
            }
    </script>
```

### 表单输入的v-model修饰符

- v-model.lazy 在 "change" 而不是 "input" 事件中更新, 不再即时更新
- v-model.number 如果想自动将用户的输入值转为 Number 类型 (如果原值的转换结果为 NaN 则返回原值)，可以添加一个修饰符 number 给 v-model 来处理输入值. 这通常很有用，因为在 type="number" 时 HTML 中输入的值也总是会返回字符串类型
- v-model.trim  如果要自动过滤用户输入的首尾空格，可以添加 trim 修饰符到 v-model 上过滤输入

## v-once 只执行一次插值

- 当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上所有的数据绑定

```html
    <span v-once>{{ msg }}</span>
```

## v-html 输出原始html

- 这个 div 的内容将会被替换成为属性值 rawHtml，直接作为 HTML —— 会忽略解析属性值中的数据绑定。注意，你不能使用 v-html 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。反之，对于用户界面 (UI)，组件更适合作为可重用和可组合的基本单位
- 站点上动态渲染的任意 HTML 可能会非常危险，因为它很容易导致 XSS 攻击。请只对可信内容使用 HTML 插值，绝不要对用户提供的内容使用插值

```html
    <div v-html="rawHtml"></div>
```

## v-on 绑定事件，事件监听

- 绑定事件可以不设置对应的响应方法

```html
    <template>
        <div id="app">
            <p>{{ message }}</p>
                <button v-on:click="reverseMessage">逆转消息</button>
                <!-- 简写 @click -->
                <!-- 传参 $event是原生事件对象-->
                <button @click="say(msg, $event)"></button>
            </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        message: 'Hello Vue.js!',
                        msg: "aaaa"
                    }
                },
                methods: {
                    reverseMessage: function (event) {
                        this.message = this.message.split(' ').reverse().join('');
                        console.log(event.target);
                    },
                    say: function (message, event) {
                        alert(message)
                    }
                }
            }
    </script>
```

## 事件修饰符

- 在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。Vue.js 为 v-on 提供了事件修饰符
- 拿click事件做例子：
  - @click.stop 阻止单击事件冒泡
  - @click.prevent 阻止默认事件
  - @click.capture 添加事件侦听器时使用事件捕获模式
  - @click.self 只当事件在该元素本身 (比如不是子元素) 触发时触发回调
  - @click.once 点击事件将只会触发一次
- 使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 @click.prevent.self 会阻止所有的点击，而 @click.self.prevent 只会阻止元素上的点击

## 键值修饰符

- `<input @keyup.13="submit">` 只有在 keyCode 是 13 时调用 vm.submit()
- keyCode别名
  - @keyup.enter
  - @keyup.tab
  - @keyup.delete (捕获“删除”和“退格”键)
  - @keyup.esc
  - @keyup.space
  - @keyup.up
  - @keyup.down
  - @keyup.left
  - @keyup.right
- 可以通过全局 config.keyCodes 对象自定义键值修饰符别名: Vue.config.keyCodes.f1 = 112  =>   可以使用 v-on:keyup.f1

## 修饰键

- 修饰键与正常的按键不同；修饰键和 keyup 事件一起用时，事件引发时必须按下正常的按键。换一种说法：如果要引发 keyup.ctrl，必须按下 ctrl 时释放其他的按键；单单释放 ctrl 不会引发事件
- .ctrl
- .alt
- .shift
- .meta
- `<input @keyup.alt.67="clear">`   <!-- Alt + C -->
- `<div @click.ctrl="doSomething">Do something</div>`  <!-- Ctrl + Click -->

## 鼠标按钮修饰符

- 这些修饰符会限制处理程序监听特定的滑鼠按键
- .left
- .right
- .middle

## v-if 条件判断

- 类似于 v-else，v-else-if 必须紧跟在 v-if 或者 v-else-if 元素之后

```html
    <template>
        <div id="app">
            <p v-if="seen==='a'">a</p>
            <p v-else-if="seen==='b'">b</p>
            <p v-else>c</p>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        seen: 'a'
                    }
                }
            }
    </script>
```

- key属性 禁止相同元素被高效的复用，区分相同的元素, 声明“这两个元素是完全独立的——不要复用它们”

```html
    <!-- 切换 loginType 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，<input> 不会被替换掉——仅仅是替换了它的 placeholder -->
    <template v-if="loginType === 'username'">
        <label>Username</label>
        <input placeholder="Enter your username">
    </template>
    <template v-else>
        <label>Email</label>
        <input placeholder="Enter your email address">
    </template>

    <!-- 只需添加一个具有唯一值的 key 属性即可：每次切换时，输入框都将被重新渲染，元素label仍然会被高效地复用，因为它们没有添加 key 属性。 -->
    <template v-if="loginType === 'username'">
        <label>Username</label>
        <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
        <label>Email</label>
        <input placeholder="Enter your email address" key="email-input">
    </template>
```

## v-show 显示隐藏元素

- 带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 是简单地切换元素的 CSS 属性 display 。
- `<template>` 不能使用 v-show

```html
    <h1 v-show="ok">Hello!</h1>
```

## v-if 和 v-show 的区别

- v-if 是“真正的”条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建
- v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块
- v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换
- 一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件不太可能改变，则使用 v-if 较好

## v-for 循环 （可以用of代替in）

- 当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素

```html
    <template>
        <div id="app">
            <ol>
                <!-- key绑定的值不要用index,要用对象自身带的唯一id -->
                <li v-for="todo in todos" :key="todo.text">
                    {{ todo.text }}
                </li>
            </ol>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        todos: [
                            { text: '1' },
                            { text: '2' },
                            { text: '3' }
                        ]
                    }
                }
            }
    </script>
```

- v-for 也可以取整数

```html
    <div>
        <span v-for="n in 10">{{ n }} </span>
    </div>
    <!-- => 1 2 3 4 5 6 7 8 9 10 -->
```

## v-for 和 v-if 比较

- 当它们处于同一节点，v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。当你想为仅有的一些项渲染节点时，这种优先级的机制会十分有用

```html
    <li v-for="todo in todos" v-if="!todo.isComplete">
        {{ todo }}
    </li>
    <!-- 代码只传递了未 complete 的 todos。 -->
```

- 如果你的目的是有条件地跳过循环的执行，那么可以将 v-if 置于外层元素

```html
    <ul v-if="todos.length">
        <li v-for="todo in todos">
            {{ todo }}
        </li>
    </ul>
    <p v-else>No todos left!</p>
```

## computed 计算属性

- 计算属性是基于它们的依赖message进行缓存的,message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数,如果是{{ reversedMessage() }}方法的话，每次访问都会重新计算，不同的场景需求不同

```html
    <template>
        <div id="app">
            <p>Original message: "{{ message }}"</p>
            <p>Computed reversed message: "{{ reversedMessage }}"</p>
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        message: 'Hello'
                    }
                },
                computed: {
                    // a computed getter
                    reversedMessage: function () {
                        return this.message.split('').reverse().join('')
                    }
                }
            }
    </script>
```

- 计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter  一般不这么做

```html
    <template>
        <div id="app">
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        firstName: 'Foo',
                        lastName: 'Bar'
                    }
                },
                computed: {
                    fullName: {
                        // getter
                        get: function () {
                            return this.firstName + ' ' + this.lastName
                        },
                        // setter
                        set: function (newValue) {
                            var names = newValue.split(' ')
                            this.firstName = names[0]
                            this.lastName = names[names.length - 1]
                        }
                    }
                }
            }
            // 现在再运行 app.fullName = 'John Doe' 时，setter 会被调用，app.firstName 和 app.lastName 也相应地会被更新。
    </script>
```

## watch 监听data某个属性的变化

- 当你想要在数据变化响应时，执行异步操作或开销较大的操作，这是很有用的, 使用 watch 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这是计算属性无法做到的
- 当监听的是引用类型的值时，应该尽量保证此类型的值每次返回的地址都是新的，避免直接写 :aa="[bb, cc]"形式

```html
    <template>
        <div id="app">
        </div>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        question: '',
                        answer: 'I cannot give you an answer until you ask a question!'
                    }
                },
                watch: {
                    // 如果 question 发生改变，这个函数就会运行 即使变化前后值还是一样
                    question: function (newQuestion, oldQuestion) {  // 默认有2个参数，新值和旧值，名字自己定
                        this.answer = 'Waiting for you to stop typing...';
                        this.getAnswer();

                        const audio = this.$refs.audio
                        this.$nextTick(() => {
                            newQuestion ? audio.play() : audio.pause()
                        }
                    },
                    // 监听对象属性的变化，但newVal和oldVal都是更新后的对象，无法知道是哪个值变化，因为它们索引同一个对象/数组。Vue 不会保留修改之前值的副本
                    plan: {
                        handler: function(newVal,oldVal) {
                            this.initDate();
                            clearInterval(this.timer);
                            this.timer = setInterval(this.changeEnable, 1000);
                        },
                        deep: true
                    },
                    // 可以用计算属性得到对象的某个属性，再监听这个计算属性；或者直接使用字符串的形式
                    'plan.id': function (newVal, oldVal) {
                        ......
                    },
                    // 或者把监听的对象指向新的对象时，newVal 就不等于 oldVal 了
                    //使用watch时有一个特点，就是当值第一次绑定时，不会执行监听函数，只有值发生改变时才会执行。如果我们需要在最初绑定值的时候也执行函数，则就需要用到immediate属性
                    plans: {
                        handler: function() {
                            this.initDate();
                            clearInterval(this.timer);
                            this.timer = setInterval(this.changeEnable, 1000);
                        },
                        // immediate为true时，元素会在绑定时就执行handler，如果是keepalive生效的组件，则切换组件时，不会反复生效immediate
                        immediate: true
                    }
                },
            }
    </script>
```

- 数组（一维、多维）的变化不需要通过深度监听，对象数组中对象的属性变化则需要deep深度监听

## $nextTick()

- JS执行是单线程的，是基于事件循环的。
- 所有同步任务都在主线程上执行，形成一个执行栈。
- 主线程之外，还存在一个任务队列。只要异步任务有了运行结果，就在任务队列之中放置一个事件。
- 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。主线程会不断重复此步骤
- 在Vue中，数据的变化后，DOM的更新会被推入到任务队列中，如需依赖更新后的DOM，则需要用`$nextTick()`

```html

<p id="text" @click="onclick">{{text}}</p>

```

```js

data () {
    return {
        text: 'a'
    };
}

methods: {
    onclick () {
        this.text = 'b';
        console.log(this.$el.textContent); // a
        this.$nextTick(function () {
            console.log(this.$el.textContent); // b
        })
    }
}

```

## directives

- 绑定自定义指令，可进行更底层的DOM操作。
- 指定提供的钩子函数：
  - bind
  - inserted
  - update
  - componentUpdated
  - unbind

- 钩子函数提供的参数：el, binding, vnode, oldVnode

```html

<script>
// 全局
Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})
</script>

<!-- 当`input`元素被插入到父节点时，为聚焦状态。 -->

<input v-model="text" v-focus>

<script>
    data() {},
    ...,
    directives: {
        focus: {
            // 指令的定义
            inserted: function (el) {
                el.focus()
            }
        }
    }

</script>

```

## 动画

- transition标签

```html
    <transition name="slide">
        <music-list :title="title" class="music-list" :bg-image="bgImage" :songs="songs"></music-list>
    </transition>
    <style>
        .slide-enter-active, .slide-leave-active {
            transition: all 0.3s;    /* 动画期间 */
        }
        .slide-enter, .slide-leave-to {
            opacity: 0; /* 不能给原样式 显式设置 opacity: 1, 否则无效  */
            transform: translate3d(100%, 0, 0);  /* 动画开始和结束位置 */
        }
    </style>
```

## 列表动画

- transition-group标签

## vue获取DOM元素的方法

```html
    <scroll class="list" ref="list"></scroll>
    <script>
        this.$refs.list.$el.style  // this.$refs.list获取的是vue对象
    </script>
```

## slot 插槽

- 子组件在父组件slot标签位置处插入

```html
    <!-- 子组件 写好模板-->
    <template>
        <header>
            <slot name="header"></slot>
        </header>
        <main>
            <!-- 父组件中未标明slot值的都使用默认slot，如果父组件没有未标明的则输出子组件的slot设置好的值 -->
            <slot>this is default content</slot>
        </main>
        <footer>
            <slot name="footer" :link="url" :text="ptext"></slot>
        </footer>
    </template>

    <!-- 父组件 -->
    <children>
        <template slot="header">
            This is header
        </template>
        <!-- slot-scope作用域插槽，声明一个参数 "slotPorps"，作为插槽的作用域,可以通过这个作用域来调用子组件传过来的数据 -->
        <template slot="footer" slot-scope="slotPorps">
            Our link: {{slotPorps.url}}
        </template>
    </children>

    <!-- 编译结果 -->
     <header>
          This is header
      </header>
      <main>
          this is default content
      </main>
      <footer>
          Our link:
      </footer>
```

## props 父子传值

```html
    <!-- 父组件 -->
    <template>
        <child :childsize="size" :childString="input" :childObj="obj" @childFunc="func"></child>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        size: 20,
                        input: 'aaa',
                        obj: {
                            a: 1,
                            b: 2
                        }
                    }
                },
                methods: {
                    func(childString) {

                    }
                }
            }
    </script>

    <!-- 子组件 -->
    <template>
        <div @click="emit"></div>
    </template>
    <script>
        export default {
                el: '#app',
                props: {
                    childsize: {
                        type: Number,
                        default: 0
                    },
                    childString: {
                        type: String,
                        default: ''
                    },
                    childObj: Object,
                    options: {
                        type: Object as () => Record<string, string>,
                        default: () => ({})
                    },
                    timelineData: {
                        type: Array as () => string[],
                        default: () => []
                    }
                },
                methods: {
                    showProps() {
                        return this.childsize
                    },
                    emit() {
                        // 父组件监听子组件派发的func函数，并获得emit时带的参数，可以从左到右选择性获取
                        // 父组件中  @func="aaa=$event"   此时$event就是子组件emit上来的第一个参数
                        this.$emit('func', this.childString, this.childsize);
                    }
                }
            }
    </script>
```

- props的几种写法

```js
props: ['name']

props: {
   name: String,

   height: {
      type: Number,
      default: 198
   }
}
```

- 如果你想要用一个对象作为 props 传递所有的属性，你可以使用不带任何参数的 v-bind (即用 v-bind 替换掉 v-bind:prop-name)。例如，已知一个 todo 对象

```html
    <!-- 父组件 -->
    <template>
        <child v-bind="todo"></child>
    </template>
    <script>
        export default {
                el: '#app',
                data() {
                    return {
                        todo: {
                            text: 'Learn Vue',
                            isComplete: false
                        }
                    }
                }
            }
    </script>
```

- props是单向数据流，子组件不能改变props值，可以使用computed处理一下props值
- 注意在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它会影响父组件的状态
- 自定义验证函数

```js
props: {
    propF: {
        type: String,
        validator: function (value) {
            return value === 'fade' || value === 'slide'
        },
        default: 'slide'
    }
}
```

- 在 default 或 validator 函数里，诸如 data、computed 或 methods 等实例属性还无法使用。

##　数据的更新检测

- 修改引用类型数据的属性时无法响应，(arr, object, map, set)
- vue.$set只能在对arr进行设值时可以触发arr的响应事件（相当于slice），尽量只用在新增属性的情况下
- 万金油解决方法

```js
// 在修改完数据后添加代码
arr = [...arr]
// 或
obj = {...obj}
```

- 变异方法：会改变这些方法调用的原数组
  - push()
  - pop()
  - shift()
  - unshift()
  - splice()
  - sort()
  - reverse()
- 非变异方法：这些不会改变原始数组，但总是返回一个新数组(watch监听这些数组时，新值和旧值会不一样，因为改变了地址)
  - filter()
  - concat()
  - slice()
- 当使用非变异方法时，可以用新数组替换旧数组

```js
    // 一个含有相同元素的数组去替换原来的数组是非常高效的操作。并且不会重新渲染整个列表
    example1.items = example1.items.filter(function (item) {
        return item.message.match(/Foo/)
    }
```

- 在Vue中，如果需要修改数组的某个元素，或者修改数组的长度，需要用vue提供的内置方法(直接修改时，数组的引用地址没有变化)。否则vue无法监听到对应的变化。
  - 利用索引直接设置一个项  

  ```js
    // 此操作无法监听
    vm.items[indexOfItem] = newValue;
    // vue内置方法修改
    Vue.set(example1.items, indexOfItem, newValue);
    example1.items.splice(indexOfItem, 1, newValue)
  ```

  - 修改数组的长度

  ```js
    // 此操作无法监听
    vm.items.length = newLength;
    // vue内置方法修改
    example1.items.splice(newLength);
  ```

- 为了响应对象属性的变化，需要用`$set`方法。

```js
vm.$set(obj, key, newValue)
```

## 显示/过滤/排序结果

```js
    // 计算属性
    data: {
        numbers: [ 1, 2, 3, 4, 5 ]
    },
    computed: {
        evenNumbers: function () {
            return this.numbers.filter(function (number) {
                return number % 2 === 0
            }
        }
    }
    // method
    data: {
        numbers: [ 1, 2, 3, 4, 5 ]
    },
    methods: {
        even: function (numbers) {
            return numbers.filter(function (number) {
                return number % 2 === 0
            }
        }
    }
```

## 过滤器

- 在一个组件的选项中定义本地的过滤器

```js
    filters: {
      capitalize: function (value) {
        if (!value) return ''
        value = value.toString()
        return value.charAt(0).toUpperCase() + value.slice(1)
      }
    }
```

- 在创建 Vue 实例之前全局定义过滤器

```js
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

- 使用

```html
<!-- 在双花括号中 -->
<!-- message是作为capitalize()的第一个参数传入 -->
{{ message | capitalize }}

<!-- a和b是分别作为capitalize()的第二个和第三个参数传入 -->
{{ message | capitalize('a','b') }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | capitalize"></div>
```

## 实例的生命周期

- 每个 Vue 实例在被创建之前都要经过一系列的初始化过程。例如需要设置数据监听、编译模板、挂载实例到DOM、在数据变化时更新DOM等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，给予用户机会在一些特定的场景下添加他们自己的代码
- 如 created、mounted、updated、destroyed等，钩子的 this 指向调用它的 Vue 实例
- 不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod()。因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，且 this.a 或 this.myMethod 也会是未定义的
- beforeCreate() {}
  - 在实例初始化之后，data、watch、methods之前。 $route对象存在，可以进行重定向
- created() {}
  - 在实例已经创建完成之后被调用. data、watcher、methods配置完成，$el挂载之前
  - watcher中的立即执行的监听，会在此之前执行
- beforeMount() {}
  - 在挂载开始之前被调用, DOM还是无法操作的
- mounted() {}
  - 在挂载之后被调用, 元素已经渲染结束
  - 如果用到了第三方UI插件，且插件需要初始化，则必须在这个阶段初始化
- activated() {}
  - keep-alive 组件激活时调用
  - 该钩子在服务器端渲染期间不被调用
- deactivated() {}
  - keep-alive 组件停用时调用
  - 该钩子在服务器端渲染期间不被调用
- beforeUpdate() {}
  - `$vm.data`更新之后，虚拟DOM重新渲染和打补丁之前被调用. 可以在这个钩子中进一步地修改`$vm.data`，这不会触发附加的重渲染过程。
- updated() {}
  - 虚拟DOM重新渲染 和打补丁之后被调用。 不能在这里修改data， 否则又触发beforeUpdate、updated这两个生命周期，进入死循环
- beforeDestroy() {}
  - 实例被销毁之前调用, 实例仍然完全可用
- destroyed() {}
  -Vue实例销毁后调用

```html
<!-- 父组件监听子组件的生命周期钩子。 -->
<child-com @hook:created="childCreated"></child-com>
```

## DOM 模板解析说明

- 当使用 DOM 作为模板时 (例如，将 el 选项挂载到一个已存在的元素上)，你会受到 HTML 的一些限制，因为 Vue 只有在浏览器解析和标准化 HTML 后才能获取模板内容。尤其像这些元素 `<ul>`，`<ol>`，`<table>`，`<select>` 限制了能被它包裹的元素，而一些像 `<option>` 这样的元素只能出现在某些元素内部。

```html
    <table>
        <my-row>...</my-row>
    </table>

    <!-- 自定义组件 <my-row> 被认为是无效的内容，因此在渲染的时候会导致错误。变通的方案是使用特殊的 is 属性： -->
    <table>
        <tr :is="my-row"></tr>  <!-- 可以用在tab栏切换 -->
    </table>
```

## vue-router 路由

- [原理](https://github.com/webfansplz/article/tree/master/webRouter)
- Vue工程中，路由的作用是通过不同的`url`映射到不同的视图，支持`hash`模式和`history`模式，默认为`hash`模式
- 参数(query)或查询(search)的改变并不会触发进入/离开的导航守卫
- `hash`模式：通过`url`的`hash`值来模拟完整的`path`
- `history`模式：通过`history.pushState`在浏览器中创建一条新的历史纪录，从而可以无需刷新页面进行跳转。可以避免显示复杂的URL
  - 你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面
  - 这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面
  
  ```js
  const router = new VueRouter({
    mode: 'history',
    routes: [
      { path: '*', component: NotFoundComponent }
    ]
  })
  ```

  - 或者如果使用 Node.js 服务器，可以用服务端路由匹配到来的 URL，并在没有匹配到路由的时候返回 404，以实现回退

- import模块后通过Vue.use(VueRouter);使用
- 通过`vm.$route`可以访问路由对象

### 路由解析流程

- 导航被触发
- 在失活的组件里调用 beforeRouteLeave 守卫
- 调用全局的 beforeEach 守卫
- 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)
- 在路由配置里调用 beforeEnter
- 解析异步路由组件
- 在被激活的组件里调用 beforeRouteEnter
- 调用全局的 beforeResolve 守卫 (2.5+)
- 导航被确认
- 调用全局的 afterEach 钩子
- 触发 DOM 更新
- 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数

### 路由匹配

- 同一个路径可以匹配多个路由, 谁先定义的，谁的优先级就最高

```js
const router = new VueRouter({
    routes: [
        {
            path: '/',
            name: 'index',
            component: Index
        },
        // 别名：/a 的别名是 /b，意味着，当用户访问 /b 时，URL 会保持为 /b，但是路由匹配则为 /a
        {
            path: '/user/:id',
            component: () => import('@/views/system/user'),
            alias: '/usercopy/:id' // 可以自由地将 UI 结构映射到任意的 URL，而不是受限于配置的嵌套路由结构
        },
        {
            path: '/article/:id/:page',
            component: Article
        },
        // 重定向： 当用户访问 /a时，URL 将会被替换成 /b，然后匹配路由为 /b
        {
            // 重定向, 需要传参
            path: '/redirect/:id',
            redirect: '/user/:id'  // 也支持 { name: 'foo' }
        },
        {
            // 重定向, 需要传参而没有传参，则不会改变地址，页面不会报错但是也不会显示内容
            path: '/redirect',
            redirect: '/user/:id'
        },
        {
            // 重定向, 动态返回重定向目标
            path: '/redirect/:id',
            redirect: to => {
                // 方法接收 目标路由 作为参数
                // return 重定向的 字符串路径/路径对象
            }
        },
        {
            path: '/product',
            name: 'product',
            component: Product,
            // 子路由
            children: [
                {
                    path: '/argus',
                    component: ProductArgus
                },
                {
                    path: '/c2-pro',
                    component: ProductC2Pro
                }
            ]
        }
    ]
});
```

#### 响应路由参数的变化

- 当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用
  - 两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用

```js
// 复用组件时
const User = {
  template: '...',

  // 第一种方法
  watch: {
    $route(to, from) {
      // 对路由变化作出响应...
    }
  }
  // 第二种方法
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

#### 路由通配符

- 当使用通配符路由时，请确保路由的顺序是正确的，也就是说含有通配符的路由应该放在最后
  - 路由 { path: '*' } 通常用于客户端 404 错误
  - 如果你使用了History 模式，请确保正确配置你的服务器, 且前端需要通配符匹配路由，返回一个404组件
  
```js
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}

// 当使用一个通配符时，$route.params 内会自动添加一个名为 pathMatch 参数。它包含了 URL 通过通配符被匹配的部分
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

### 全局方法

- to: Route: 即将要进入的目标 路由对象 $route
- from: Route: 当前导航正要离开的路由对象 $route
- next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数
  - next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)
  - next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址
  - next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在`router-link`的`to`prop 或 router.push 中的选项
  - next(error): 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调

- 全局守卫

```js
// 全局前置守卫
// 当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中
// router 是 Router的实例
router.beforeEach((to, from, next) => {
    // ...
    next();
});
// 全局解析守卫
// 在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用
router.beforeResolve((to, from, next) => {
    // ...
    next();
});
// 全局后置钩子
router.afterEach((to, from) => {
    // ...
    // 不会接受 next 函数也不会改变导航本身
});
```

- 路由独享的守卫

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo/:id',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // to 的参数举例
        //   {
        //       hash: '',
        //       fullPath: '/product/argus',
        //       path: '/product/argus',
        //       name: 'product.argus',
        //       matched: [{...}, {...}],
        //       meta: {
        //           needLogin: true,
        //           mustAgent: true
        //       },
        //       params: {id: 222},
        //       query: {aa: 'bbb'}
        //   }
      }
    }
  ]
})
```

- 组件内的路由守卫(写法同生命周期钩子)

```js
// 在渲染该组件的对应路由在 confirm 前调用
beforeRouteEnter (to, from, next) {
    // 不！能！获取组件实例 `this`，因为当守卫执行前，组件实例还没被创建。(在组件周期钩子函数beforeCreate前执行)
    // 可以通过传一个回调给 next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数
    next(vm => {
        // 通过 `vm` 访问组件实例
    });
    // 这是能给 next 传递回调的唯一守卫
}

// 在当前路由改变，但是该组件被复用时调用
beforeRouteUpdate (to, from, next) {
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
    next();
}

beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
    next();
}
```

- 离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消

```js
beforeRouteLeave (to, from , next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

### 路由元信息

- `routes`配置中的每个路由对象为`路由记录`,路由记录可以是嵌套的，因此，当一个路由匹配成功后，他可能匹配多个路由记录
  - 即如果路由是‘/foo/bar’的话，‘/foo’也会触发
- 一个路由匹配到的所有路由记录会暴露为 $route 对象 (还有在导航守卫中的路由对象) 的 $route.matched 数组。因此，我们需要遍历 $route.matched 来检查路由记录中的 meta 字段
  - 如果使用 $route.meta.requiresAuth 来检查，则对应路由及其所有子路由，都必须要加上requiresAuth字段，才能确保都是有登录校验的，而使用 $route.matched 可以遍历所有匹配到的路由，所以只需要最高级的路由加上requiresAuth字段即可

```js
// `/foo/bar` 这个 URL 将会匹配父路由记录以及子路由记录
const router = new VueRouter({
  routes: [
    {
        path: '/foo',
        component: Foo,
        // 定义路由的时候可以配置 meta 字段
        meta: { requiresAuth: true }
        children: [
            {
                path: '/bar',
                component: Bar
            },
            {
                path: '/baraa',
                component: Bar
            }
        ]
    }
  ]
})

router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        // 把要跳转的地址作为参数传到下一步 例如：/login?redirect=%2Fa, 在登录页登录成功后即可利用query跳转
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next() // 确保一定要调用 next()
  }
})
```

### 路由参数 props

- 一般我们获取路由的参数是通过 this.$route.params 来获取
- 让组件和路由解耦，尽量不要在组件中使用 $route,$router 方法
- 三种写法

```javascript
const router = new VueRouter({
    routes: [
        {
            path: '/',
            name: 'index',
            component: index
        },

        // 对应test组件中 props: ['id', 'page'] 来引入路由中的值
        {
            path: '/test/:id/:page',
            name: 'test',
            component: test,
            props: true
        },

        // 对应 login组件中 props：["country"，“time”] 来引入路由配置中的设置的静态值
        {
            path: '/login/:id/:page',
            name: 'login',
            component: login,
            props: {
                country: 'China',
                time: '6pm'
            }
        },

        // 对应 logout 组件中 props：["country"，“time”] 来引入路由配置中的设置的值
        {
            path: '/logout/123?a=234&b=345',
            name: 'logout',
            component: logout,
            props: (route) => ({
                    // 可以将静态值与基于路由的值结合
                    country: route.query.a,
                    time: route.query.b
                })
        }
    ]
})


```

### 路由跳转

- 路由切换时，当前router-link标签会自动加上router-link-active类

 ```html
    <!-- 这个会生成div标签 -->
    <router-link tag="div" to="/product/">User</router-link>
    <!-- 默认生成a标签 -->
    <router-link :to="{ name: 'user', params: { id: 123 }}">User</router-link>
    <router-link :to="{ name: 'product'}">product</router-link>
    <router-link to="/product/">product</router-link>

    <div>
        <!-- 界面上多个router-view时跳转路由会发生什么 ？？？？？？ -->
        <router-view></router-view>
    </div>
 ```

 ```js
    // 通过方法进行路由跳转
    router.push({ name: 'user', params: { id: 123 } })
    router.push({ name: 'product'})
    router.push('/product/')
 ```

- keep-alive 是Vue的内置组件，能在组件切换过程中将状态保留在内存中，防止重复渲染DOM。一般App.vue使用
  - 当目前激活的组件不是对应某个组件时，该组件的监听依旧在执行
    - 如A组件监听了store中的time值，当切换tab到B组件，B组件修改了store中的time值，A组件的监听依旧会被触发

 ```html
    <keep-alive>
      <router-view></router-view>  <!-- 当前router-link所对应显示的view -->
    </keep-alive>
 ```

- 每次路由切换时，对应页面都会执行钩子destroyed(),此时可以清掉计时器等相关操作 destroyed() {clearTimeout(this.timer)}
  <!-- - 在keep-alive组件中也会执行destroyed()吗？？？？？？ -->

### 动态路由

- 通过 Router.addRoutes([...routes]) 动态添加路由配置，一般用在权限管理时
  - 应用初始化的时候先挂载不需要权限控制的路由，比如登录页，404等错误页
- 权限管理中遇到的问题：路由添加成功，并且能正常的跳转。但是，当我刷新页面后页面路由出错了，直接进入了错误页面404
  - 原因：vue在初始化的时候，vue-router的实例对象已经生成了，当前路由只包含了如登录页和404等静态页面，用户权限获取的路由需要在登录成功后单独请求权限接口再动态添加到路由当中，所以页面在刷新的时候页面会找不到对应动态的路由
- 权限管理思路：在路由的导航守卫router.beforeEach中判断当前是页面刷新还是路由切换，利用vuex的状态值在页面刷新就会重新初始化的特性和本地缓存localStorage存的登录状态值来判断页面刷新还是路由切换
- 权限管理的实现：
  - 1. 用户登录—> 本地缓存用户token和id，vuex中存入登录用户id（存的值,对还是错都不重要，值只是用来判断页面刷新还是路由切换，随意存。。只是用于后续的判断） —>接口获取当前用户的菜单信息—>格式化菜单信息（格式化成，路由格式）—>使用router.addRoutes动态加入路由
  - 2. 在导航守卫router.beforeEach的方法中，进入路由后，判断 1、本地缓存的token是否有值，没有的话，说明用户没有登录过，直接跳转登录页面 2、token值有的，vuex中的用户id存在的话，页面只是路由切换，用next让路由正常流转下去 2、token值有的，vuex中的用户id的值不存在的话，说明页面刷新了，这个时候需要做两个件事情，A：设置vuex中的id的为本地缓存的用户id ， B：重新请求权限接口，格式化后，动态添加到路由，然后重新用next实现接下来的路由跳转，这里需要注意，请求的权限接口需要用async/wait 改成同步接口方便使用

### 路由懒加载

- 即路由配置中的component使用的是 () => import( './Foo.vue') 方式按需加载，当路由被访问时才加载对应的组件
- 把某个路由下的所有组件都打包在同个异步块 (chunk) 中, 把组件按组分块

```js
// 注释语法会将这三个模块都打包到同一个异步块(chunk)中
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

## mounted 严格保证初始化成功挂载应用

 ```js
    // 计时器相关操作，尽量都用setTimeout，而不用setInterval
    mounted() {
        setTimeout(() => {
            this._initScroll()
        }, 20)
    },
    watch: {
        data() {
            setTimeout(() => {
                this._calculateHeight()
            }, 20)  // 延时20毫秒确保挂载完成在进行初始化   一般浏览器每秒钟进行60次重绘，约16.67毫秒刷新一次
        },
    }
 ```

## vuex

- [原理](https://zhuanlan.zhihu.com/p/78981485)

### state.js

 ```js
    const state = {
        singer: {},
        playing: false,
        fullScreen: false,
    }

    export default state
 ```

### getters.js

 ```js
    export const singer = state => state.singer
    export const playing = state => state.playing
    export const fullScreen = state => state.fullScreen
 ```

### mutation-types.js

 ```js
    export const SET_SINGER = 'SET_SINGER'
    export const SET_PLAYING_STATE = 'SET_PLAYING_STATE'
    export const SET_FULL_SCREEN = 'SET_FULL_SCREEN'
 ```

### mutations.js

 ```js
    import * as types from './mutation-types'

    const mutations = {
        [types.SET_SINGER](state, singer) {
            state.singer = singer
        },
        [types.SET_PLAYING_STATE](state, flag) {
            state.playing = flag
        },
        [types.SET_FULL_SCREEN](state, flag) {
            state.fullScreen = flag
        }
    }

    export default mutations
 ```

### actions.js

 ```js
    import * as types from './mutation-types'
    export const selectPlay = function ({commit, state}, {list, index}) {
        commit(types.SET_SEQUENCE_LIST, list)
        if (state.mode === playMode.random) {
            let randomList = shuffle(list)
            commit(types.SET_PLAYLIST, randomList)
            index = findIndex(randomList, list[index])
        } else {
            commit(types.SET_PLAYLIST, list)
        }
        commit(types.SET_CURRENT_INDEX, index)
        commit(types.SET_FULL_SCREEN, true)
        commit(types.SET_PLAYING_STATE, true)
    }
 ```

### index.js

 ```js
    import Vue from 'vue'
    import Vuex from 'vuex'
    import * as actions from './actions'
    import * as getters from './getters'
    import state from './state'
    import mutations from './mutations'
    import createLogger from 'vuex/dist/logger'

    Vue.use(Vuex)

    const debug = process.env.NODE_ENV !== 'production'

    export default new Vuex.Store({
        actions,
        getters,
        state,
        mutations,
        strict: debug,
        plugins: debug ? [createLogger()] : []
    })
 ```

### 组件中取store文件值

```js

computed: {
    count(): {
        return this.$store.state.count;
    },
    num(): {
        return this.$store.getters.num;
    }
},
methods: {
    setMum() {
        this.$store.commit('SET_SINGER', singer)
    },

    setMums() {
        this.$store.dispatch( 'selectPlay', {list, index} )
    }
}

```

### 使用map系列方法时组件中写法

 ```js
    import {mapGetters, mapMutations, mapActions} from 'vuex'

    export default {
        computed: {
            ...mapGetters([
                'playHistory' // this.playHistory调用
            ])
        },

        methods: {
            ...mapMutations({
                setPlayMode: 'SET_PLAY_MODE'  // this.setPlayMode()调用
            }),
            ...mapActions([
                'insertSong' // this.insertSong()调用
            ])
        }
    }

 ```

 computed: {
     ...mapGetters([
         'play'
     ])
 },
 methods: {
     ...mapMutations({
         set: 'SET'
     }),
     ...mapActions([
         'inset'
     ])
 }

### modules 文件

- 在store文件夹下新建modules文件，内部新建各个不同功能的store文件，也就是把一类的状态管理写在一个文件中，方便维护
- 一个模块文件可以包含state、mutation、getters、actions全部或者部分，不包含的部分可以使用外部的文件

```js
    // lang.js 模块
    import { SELECT_LANG } from '../mutation-types';
    const state = {

        lang: 'de'

    };

    const mutations = {

        [ SELECT_LANG ] ( state, lang ) {

            state.lang = lang;

        }

    };

    const getters = {

        lang: state => state.lang

    };

    export default {

        state,
        mutations,
        getters

    };

    // 外部公共的actions文件
    import * as types from './mutation-types';

    export const closeTopInfo = function ({commit}) {

        commit( types.CLOSETOPINFO );
        commit( types.OPENWILLSHOW );

    };

    // index.js文件
    import Vue from 'vue';
    import Vuex from 'vuex';
    import * as actions from './actions';
    import createLogger from 'vuex/dist/logger';
    import lang from './modules/lang';
    import topInfo from './modules/topInfo';

    Vue.use( Vuex );

    const debug = process.env.NODE_ENV !== 'production';

    export default new Vuex.Store({

        actions,
        modules: {

            user,
            topInfo

        },
        strict: debug, // 严格模式下，不是mutation函数引起的状态变化都会抛出错误, 正式版本不能开启严格模式
        plugins: debug ? [createLogger()] : []

    });
    // 取值时得用this.$store.state.user获取到模块user的state对象
    // 因为getters是全局使用的，所以this.$store.getters获取到的是所有模块合在一起的state对象
```

### vue2.0 + ts 中的 vuex写法

```ts
// baseConfig.ts
import * as Vuex from 'vuex';

export interface BaseConfig {
    dialogId: string;
}

const state: BaseConfig = {
    dialogId: ''
}

const mutations = {
    SET_DIALOGID(state: BaseConfig, dialogId: string) {
        state.dialogId = dialogId;
    }
}

const getters = {

    dialogId: (state: BaseConfig) => state.dialogId
}

const actions = {

    handleDialogId(
        actionContext: Vuex.ActionContext<BaseConfig, BaseConfig>,
        // 需要传多个属性值时就需要以对象的形式传递，因为actions方法只能有两个参数
        paras: {
            dialogId: string
        }
    ) {
        actionContext.commit('SET_DIALOGID', paras.dialogId);
    }
}

export default {
    state,
    getters,
    mutations,
    actions
};

// index.ts
import Vue from 'vue';
import * as Vuex from 'vuex';
import baseConfig, { BaseConfig } from './modules/baseConfig';
import createLogger from 'vuex/dist/logger';

Vue.use(Vuex);

const debug = process.env.NODE_ENV !== 'production';

export interface State {
    baseConfig: BaseConfig;
}

export default new Vuex.Store<State>({
    modules: {
        baseConfig
    },
    strict: debug,
    plugins: debug ? [createLogger({})] : []
});
```

## 语言插件 vue-i18n

### 安装插件

```js
Vue.use(VueI18n);

const i18n = new VueI18n({
    // 默认语言
    locale: 'en-US',
    // 以下为属性
    messages: {
        'en-US': {
            hi: 'Hi',
            plan: 'Cloud {planname}',
            cycle: 'Daily | {count}-Day'
        },
        'zh-CN': {
            hi: '你好'
        },
        fr: require('./common/lang/fr')
    },
    numberFormats: {
        'en-US': {
            decimal: {
                style: 'decimal'
            }
        },
        'zh-CN': {
            decimal: {
                style: 'decimal'
            }
        }
    }

});

new Vue({
    el: '#app',
    router,
    components: { App },
    template: '<App/>',
    i18n,
    store,
    render: h => h(App),
}).$mount('#app')


```

### 使用vue-i18n

```html

<!-- 一般使用 -->
<p>{{ $t('hi') }}<p>

<!-- 传入字符串 -->
<p>Hot Sales: {{ $t('plan', {planname: 'Basic plan'}) }}</p>

<!-- 区分单复数 -->
<!-- 根据第二个参数传入的值选用 'Daily | {count}-Day' 里面的值，单数选第一个，复数选第二个格式（完整的为 a | b | c， 传 0 则使用 a , 传 1 则使用 b， 复数使用 c） -->
<p>Cycle: {{ $tc('cycle', 20, {count: 20}) }}<p>

<!-- 数字本地化 -->
<!-- 例如10000.69在德语中显示 10.000,69，在英语中显示10,000.69 -->
<p>In stock: {{ $n(10000.69) }}</p>

```

- 注意，存储i18n文案的变量，需要是计算属性或者是一个返回文案的方法，才能即时响应语言的切换
- 注意$tc()方法的使用

```js
// vue-i18n.js
function loadLocaleMessages(): LocaleMessages {
    const defaultLocales = require.context('@/assets/locales', true, /[A-Za-z0-9-_,\s]+\.json$/i);
    let locales: __WebpackModuleApi.RequireContext;
    if (CONFIG.IS_CLIENT) {
        try {
            if (process.env.VUE_APP_APP_PROTOCOL !== 'reolink') {
                locales = require.context(`@src/renderer/assets/locales-${process.env.VUE_APP_APP_PROTOCOL}`, true, /[A-Za-z0-9-_,\s]+\.json$/i);
            }
        } catch (error) {
            //
        }
    }
    const messages: LocaleMessages = {};
    defaultLocales.keys().forEach(key => {
        const matched = key.match(/([A-Za-z0-9-_]+)\./i);
        if (matched && matched.length > 1) {
            const locale = matched[1];
            (messages as any)[locale] = locales !== undefined && locales.keys().includes(key) ? Object.assign(defaultLocales(key), locales(key)) : defaultLocales(key);
        }
    });
    return messages;
}

export default new VueI18n({
    locale: 'en',
    fallbackLocale: 'en',
    messages: loadLocaleMessages()
});
```
