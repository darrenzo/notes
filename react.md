# React

- [官网](https://reactjs.org/)
- React 是一个用于构建用户界面的js库
- React的核心机制之一就是可以在内存中创建虚拟的DOM元素。React利用虚拟DOM来减少对实际DOM的操作从而提升性能
- React模拟事件系统
  - React并不会真正的绑定事件到每一个具体的元素上，而是采用事件代理的模式：在根节点document上为每种事件添加唯一的Listener，然后通过事件的target找到真实的触发元素。这样从触发元素到顶层节点之间的所有节点如果有绑定这个事件，React都会触发对应的事件处理函数
  - 尽管整个事件系统由React管理，但是其API和使用方法与原生事件一致
- vscode语言高亮插件`sublime-babel-vscode`
- [开发调试器](https://github.com/facebook/react-devtools)

## Next

- [官网](https://nextjs.org/)
- 用于使用React构建静态和服务器呈现的应用程序的轻量级框架
- 包括开箱即用的样式化和路由解决方案，使用node.js作为服务器环境

## Gatsby

- [官网](https://www.gatsbyjs.org/)
- 用React创建静态网站的最佳方式
- 允许使用React组件
- 输出预渲染的HTML和CSS以保证最快的加载时间
- [gallery of starter kits](https://www.gatsbyjs.org/docs/gatsby-starters/)

## Neutrino

- [官网](https://neutrinojs.org/)
- 结合了webpack的强大功能和简单的预置功能，并为React应用和React组件提供了预置功能
  
## nwb

- [官网](https://github.com/insin/nwb)
- 特别适合发布npm的React组件。它也可以用来创建React应用

## Parcel

- [官网](https://parceljs.org/)
- 一个快速、零配置的web应用程序绑定器，可以与React一起工作

## Razzle

- [官网](https://github.com/jaredpalmer/razzle)
- 一个服务器呈现框架，不需要任何配置，但提供了比Next.js更大的灵活性

## JSX语法简述

- JSX就是Javascript和XML结合的一种格式
- React发明了JSX，利用HTML语法来创建虚拟DOM
- 当遇到`<`，JSX就当HTML解析，遇到`{`就当JavaScript解析
- JSX只是创建虚拟DOM的一种语法格式, 在产品打包阶段都已经编译成纯JavaScript
- 除了用JSX,我们也可以用JS代码来创建虚拟DOM
- jsx中，React DOM 使用驼峰方式命名html元素的属性，比如`class`写成`className`，`tabindex`写成`tabIndex`

```js
var child1 = React.createElement('li', null, 'First Text Content');
var child2 = React.createElement('li', null, 'Second Text Content');
var root = React.createElement('ul', { className: 'my-list' }, child1, child2);
```

`equal`

```jsx
var root =(
  <ul className="my-list">
    <li>First Text Content</li>
    <li>Second Text Content</li>
  </ul>
);
```

### 使用

- 简单的使用，但不要在生产环境用,在网页上进行jsx编译浪费性能
- crossorigin属性是为了使用cdn

```html
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script crossorigin src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

<script type="text/babel">

  ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('root')
  );

</script>
```

- 安装jsx预编译器
  - npm init -y (如果失败，就需要将项目文件夹重命名为只包含小写ASCII字母或连字符(例如my-project)，然后重试)
  - npm install babel-cli@6 babel-preset-react-app@3
  - npx babel --watch src --out-dir . --presets react-app/prod

### 花括号

```jsx
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### 嵌入函数

```jsx
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

// 此处使用花括号执行一个表达式时，就不能再使用引号
// jsx声明element时常常使用圆括号来更好的区分
const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

- 或者使用函数判断返回element

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### JSX三元表达式

```jsx
const sign = (
  <h1 className={window.isLoggedIn ? window.name : ''}>
    Hello, world!
  </h1>
);
```

`经过JSX编译后`

```js
// jsx声明element实际上使用的都是React.createElement()方法
const sign = React.createElement(
  'h1',
  {className: window.isLoggedIn ? window.name : ''},
  'Hello, world!'
);
```

`本质上是`

```js
// 简化写法，组件传值的依据
const sign = {
  type: 'h1',
  props: {
    className: window.isLoggedIn ? window.name : '',
    children: 'Hello, world!'
  }
};
```

### 数组循环,数组的每个元素都返回一个React组件

```jsx
var lis = this.todoList.todos.map(function (todo) {
  return  (
    <li>
      <input type="checkbox" checked={todo.done}>
      <span className={'done-' + todo.done}>{todo.text}</span>
    </li>
  );
});

var ul = (
  <ul className="unstyled">
    {lis}
  </ul>
);
```

### JSX中绑定事件

- 和原生HTML定义事件的唯一区别就是JSX采用驼峰写法来描述事件名称

```jsx
<button onClick={this.checkAndSubmit.bind(this)}>Submit</button>
```

### JSX中使用样式

- 在JSX中同样通过style属性来定义，但和真实DOM不同的是，属性值不能是字符串而必须为对象

```jsx
var style2 = {
  color: '#ff0000',
  // 基本上属性名的转换规范就是将其写成驼峰写法
  fontSize: '14px'
}

<div style={{color: '#ff0000', fontSize: '14px'}}>Hello World.</div>
<div style={style2}>Hello World2.</div>
```

### html转义

- 在组件内部添加html代码,并将html代码渲染到页面上。React默认会进行HTML的转义，避免XSS攻击

```jsx
// 不进行html转义的写法
var content='<strong>content</strong>';
React.render(
    <div dangerouslySetInnerHTML={{__html: content}}></div>,
    document.body
);
```

## 新建单页面 React App

```shell
npx create-react-app my-app
cd my-app
npm start
```

## 创建react元素

```html
<div id="root"></div>
```

```jsx
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

## 更新react元素

- 已创建的react元素是不可变的，包括不能修改属性和子代元素
- 唯一方式就是创建一个新的react元素，进行render

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

// 基本上不会通过时间去render
setInterval(tick, 1000);
```

## 组件

- 概念上讲，组件就是js函数
- 组件接收任意props，并返回界面应该出现的react元素
- 接收的props都是只读的
- 保证组件名首字母大写
  - `<div />`小写的react一般视作DOM标签
  - `<Welcome />`首字母大写的视作组件

### 函数组件和类组件

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

`或者es6写法`

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### 渲染组件

- `const element = <div />;` 创建一个普通的DOM tag
- `const element = <Welcome name="Sara" />;` 引用一个用户自定义的组件

```jsx
// 创建一个名为Welcome的组件
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// 引入Welcome组件，并传入 {name: 'Sara'} 作为props
const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

`rendered`

```html
<h1>Hello, Sara</h1>
```

- 组件可以套用别的组件的返回值
- 这使得一个大组件可以由多个小组件组成

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

- 所有的React组件都必须像纯函数(不对props进行修改)那样操作它们的props
- `this.props`获取数据

### 状态组件

- 每次发生更新时都会调用render方法，但是只要呈现到相同的DOM节点中，就只使用Clock类的一个实例。这允许我们使用其他特性，如本地状态和生命周期方法。

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

- 加入本地状态和声明周期函数
- 如果用到了constructor就必须写super(),是用来初始化this的，可以绑定事件到this上
- 如果在constructor中要使用this.props,就必须给super加参数：super(props)
- 无论有没有constructor，在render中this.props都是可以使用的，这是React自动附带的
- 如果没用到constructor,是可以不写的

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  // DOM元素创建后
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  // DOM元素销毁前
  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    // 更新数据用setState方法
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

- 数据更新有可能是异步的，所以不要依赖`this.props`和`this.state`的值来计算下一个状态
- 向setState方法传入一个函数，该函数将接收前一个状态state作为第一个参数，更新后的props作为第二个参数

```jsx
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

`instead`

```jsx
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

- 父子传值

```jsx
function FormattedDate(props) {
  return <h2>It is {props.childDate.toLocaleTimeString()}.</h2>;
}

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <FormattedDate childDate={this.state.date} />
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

## 处理事件

- react组件中事件名得用驼峰命名
- 用一个函数而不是字符串作为事件处理器

```html
<!-- 普通html -->
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

```html
<!-- react -->
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

- 不能通过`return false`来阻止默认事件
- 需要明确的通过`e.preventDefault();` 来阻止默认事件

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

## 方法的this问题

- 在JavaScript中，默认情况下不绑定类方法
- 在constructor中进行绑定

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props); // ？？？？？？
    this.state = {isToggleOn: true};

    // 如果这里不绑定this,render中this将是undefined
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

- class fields syntax

```jsx
class LoggingButton extends React.Component {
  // Warning: this is *experimental* syntax.？？？？？
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

- 箭头函数
  - 这种方法的问题：每次重新render时都会创建不同的回调，当这个回调是作为props进行父子传值时，会让子组件跟着进行额外的render ？？？？

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

- 传参
  - 表示React事件的e参数都将作为ID之后的第二个参数传递
  - 使用箭头函数时，必须要显示的指明`e`
  - 使用`bind`方法时，则自动转发

```html
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

## 条件渲染

```jsx
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;

    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```

- 行内if判断写法
  - `true` && `expression` 返回 `expression`
  - `false` && `expression` 返回 `false`

```jsx
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

`三元运算符`

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}

// 或者

render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```

- 如果组件需要按条件判断是否render，可以`return null`
- 从一个组件的render函数`return null`,不会影响其生命周期函数的触发

```jsx
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}
```

## 渲染多个重复组件

- 给每个循环生成的元素加上key属性，是为了react能很好的区分，如果不显示的声明key属性，react默认使用遍历函数的参数中的index作为key值
- 避免使用遍历函数的参数中的index作为key值，因为数组的顺序一旦发生改变，会给性能带来消极的影响和额外的问题
- [关于key值的详细说明](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

- 抽取遍历的重复组件时，不要把key值也抽取出来

```jsx
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

- 元素的key属性值在其兄弟元素范围内应该是唯一的
- key值只是给react提供一个区分的提示，不会传值给组件使用

```jsx
// 组件能读取 props.id，但不能读取 props.key
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

## 受控组件

- 由React控制其值的输入表单元素称为“受控组件”
- 包括验证、跟踪访问的字段和处理表单提交的完整解决方案之一--[Formik](https://jaredpalmer.com/formik)

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'Please write an essay about your favorite DOM element.'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    // 此处可以对value值进行操作，比如event.target.value.toUpperCase()
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          // this.state.value是在constructor初始化的，所以页面一加载this.state.value就会有值
          <input type="text" value={this.state.value} onChange={this.handleChange} />
          // <textarea value={this.state.value} onChange={this.handleChange} /> textarea也可以按这样写
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

- select组件

```jsx
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          // select的 value值就是选中的option值
          // <select multiple={true} value={['B', 'C']}> 多选框
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

- file input 标签的value是只读的，所以是不受控组件
- 同时处理多个inputs

```jsx
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value // ？？？？？？
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

- 给受控组件指定明确的val值会阻止用户更改input, 而设置val值为`undefined` 或者 `null`则不会 ？？？？

```jsx
// 用户无法更改输入
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  // 用户可更改输入
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```

- react的数据流是从上到下的
- 提升公共状态到公共父组件,实现不同组件间的数据同步

```jsx

const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};

function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>The water would boil.</p>;
  }
  return <p>The water would not boil.</p>;
}

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    // 子组件触发方法，并携带数据
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}

class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = {temperature: '', scale: 'c'};
  }

  // 子组件通过触发父组件的这个方法改变公共状态
  handleCelsiusChange(temperature) {
    this.setState({scale: 'c', temperature});
  }

  // 子组件通过触发父组件的这个方法改变公共状态
  handleFahrenheitChange(temperature) {
    this.setState({scale: 'f', temperature});
  }

  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    // 只在应用到界面前对原始数据进行加工
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange} />
        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange} />
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    );
  }
}

ReactDOM.render(
  <Calculator />,
  document.getElementById('root')
);

```

## 组件间的合成和继承

- 继承极不推荐用，用合成就已能完全满足需要
- props可以是任意的数据，包括值、元素或者方法
- 如果想在多个组件间复用没有UI方面的功能，应该提取到单独的js模块中，通过import使用，而不是通过继承

### 组件的pros.children (类似于slot)

```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children} //  h1标签和 p标签
    </div>
  );
}

function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

- 同时插入多个子组件

```jsx
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

### 泛用组件具体化(通过传props)

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />

        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```

## 编写无障碍环境网站

### [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/)

- 提供创建可访问web站点的指南
- 主要的检查列表
  - [WCAG checklist from Wuhcag](https://www.wuhcag.com/wcag-checklist/)
  - [WCAG checklist from WebAIM](https://webaim.org/standards/wcag/checklist)
  - [Checklist from The A11Y Project](https://a11yproject.com/checklist.html)
  
### [WAI-ARIA](https://www.w3.org/WAI/intro/aria)

- 所有的`aria-*`这种HTML属性是JSX完全支持的，而且不用使用驼峰写法

```jsx
<input
  type="text"
  aria-label={labelText}
  aria-required="true"
  onChange={onchangeHandler}
  value={inputValue}
  name="name"
/>
```

### HTML语义化

- 语义化标签
  - [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)

### 语义化的Forms

- 资源
  - [The W3C shows us how to label elements](https://www.w3.org/WAI/tutorials/forms/labels/)
  - [WebAIM shows us how to label elements](https://webaim.org/techniques/forms/controls)
  - [The Paciello Group explains accessible names](https://www.paciellogroup.com/blog/2017/04/what-is-an-accessible-name/)
- 元素的`for`属性在JSX中应该写成`htmlFor`

```jsx
<label htmlFor="namedInput">Name:</label>
<input id="namedInput" type="text" name="name"/>
```

### 错误提示

- 正确的展现错误场景
  - [The W3C demonstrates user notifications](https://www.w3.org/WAI/tutorials/forms/notifications/)
  - [WebAIM looks at form validation](https://webaim.org/techniques/formvalidation/)

### web app关于键盘操作的注意点

- [WebAIM talks about keyboard accessibility](https://webaim.org/techniques/keyboard/)

### 跳转到所需内容的机制

- 提供一种机制，允许用户跳过应用程序中的导航部分，因为这有助于加快键盘导航
  - [WebAIM - Skip Navigation Links](https://webaim.org/techniques/skipnav/)
  - [Accessible Landmarks](https://www.scottohara.me/blog/2018/03/03/landmarks.html)

### 以编程方式管理聚焦

- React应用程序在运行期间不断修改HTML DOM，有时会导致键盘焦点丢失或设置为一个意外的元素。为了修复这个问题，我们需要通过编程将键盘焦点移向正确的方向。例如，通过将键盘焦点重置为在关闭模式窗口后打开模式窗口的按钮
- [keyboard-navigable JavaScript widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)
- [React: Refs to DOM elements](https://reactjs.org/docs/refs-and-the-dom.html)

### 鼠标和键盘事件

- 确保通过鼠标或指针事件公开的所有功能也可以仅使用键盘访问。仅依赖于指针设备将导致键盘用户无法使用您的应用程序的许多情况

```jsx
// 每一行组件
// 点击列表的li可以显示对应的详情，而点页面空白处可以隐藏详情，但是一定要使用点击
class OuterClickExample extends React.Component {
constructor(props) {
    super(props);

    this.state = { isOpen: false };
    this.toggleContainer = React.createRef();

    this.onClickHandler = this.onClickHandler.bind(this);
    this.onClickOutsideHandler = this.onClickOutsideHandler.bind(this);
  }

  componentDidMount() {
    window.addEventListener('click', this.onClickOutsideHandler);
  }

  componentWillUnmount() {
    window.removeEventListener('click', this.onClickOutsideHandler);
  }

  onClickHandler() {
    this.setState(currentState => ({
      isOpen: !currentState.isOpen
    }));
  }

  onClickOutsideHandler(event) {
    if (this.state.isOpen && !this.toggleContainer.current.contains(event.target)) {
      this.setState({ isOpen: false });
    }
  }

  render() {
    return (
      <div ref={this.toggleContainer}>
        <button onClick={this.onClickHandler}>Select an option</button>
        {this.state.isOpen ? (
          <ul>
            <li>Option 1</li>
            <li>Option 2</li>
            <li>Option 3</li>
          </ul>
        ) : null}
      </div>
    );
  }
}
```

```jsx
// 每一行组件
class BlurExample extends React.Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
    this.timeOutId = null;

    this.onClickHandler = this.onClickHandler.bind(this);
    this.onBlurHandler = this.onBlurHandler.bind(this);
    this.onFocusHandler = this.onFocusHandler.bind(this);
  }

  onClickHandler() {
    this.setState(currentState => ({
      isOpen: !currentState.isOpen
    }));
  }

  // 在按钮间连续切换时，持续设置isopen为false
  onBlurHandler() {
    this.timeOutId = setTimeout(() => {
      this.setState({
        isOpen: false
      });
    });
  }

  // 还没写键盘事件来支持弹出窗口选项的箭头键交互

  // 如果持续focus一个按钮时，则清除timeout
  onFocusHandler() {
    clearTimeout(this.timeOutId);
  }

  render() {
    // React 冒泡blur和focus事件到父元素
    return (
      <div onBlur={this.onBlurHandler}
           onFocus={this.onFocusHandler}>

        <button onClick={this.onClickHandler}
                aria-haspopup="true"
                aria-expanded={this.state.isOpen}>
          Select an option
        </button>
        {this.state.isOpen ? (
          <ul>
            <li>Option 1</li>
            <li>Option 2</li>
            <li>Option 3</li>
          </ul>
        ) : null}
      </div>
    );
  }
}
```

### 更为复杂的小工具

- [ARIA Roles](https://www.w3.org/TR/wai-aria/#roles)
- [ARIA States and Properties](https://www.w3.org/TR/wai-aria/#states_and_properties)
- 例子
  - [WAI-ARIA Authoring Practices - Design Patterns and Widgets](https://www.w3.org/TR/wai-aria-practices/#aria_ex)
  - [Heydon Pickering - ARIA Examples](https://heydonworks.com/practical_aria_examples/)
  - [Inclusive Components](https://inclusive-components.design/)

### 屏幕阅读器正确识别语言

- 显示页面文本的人类语言，因为屏幕阅读器软件使用它来选择正确的语音设置
  - [WebAIM - Document Language](https://webaim.org/techniques/screenreader/#language)

### 正确设置文档title

- [WCAG - Understanding the Document Title Requirement](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-title.html)
- [tool: React Document Title Component](https://github.com/gaearon/react-document-title)

### 色差

- 确保你网站上所有可读的文字都有足够的颜色对比度，以保持低视力用户的最大可读性
- [WCAG - Understanding the Color Contrast Requirement](https://www.w3.org/TR/UNDERSTANDING-WCAG20/visual-audio-contrast-contrast.html)
- [Everything About Color Contrast And Why You Should Rethink It](https://www.smashingmagazine.com/2014/10/color-contrast-tips-and-tools-for-accessibility/)
- [A11yProject - What is Color Contrast](https://a11yproject.com/posts/what-is-color-contrast/)
- [tool: calculate an entire accessible color palette with Colorable](https://jxnblk.com/colorable/)
- 颜色对比测试工具
  - [WebAIM - Color Contrast Checker](https://webaim.org/resources/contrastchecker/)
  - [The Paciello Group - Color Contrast Analyzer](https://www.paciellogroup.com/resources/contrastanalyser/)

### 开发和测试工具

#### 键盘

- 到目前为止，最简单也是最重要的检查之一就是测试你的整个网站是否可以通过键盘访问和使用
  - 拔掉鼠标
  - 使用Tab和Shift+Tab来浏览
  - 使用Enter来激活元素
  - 在需要的地方，使用键盘箭头键与一些元素交互，比如菜单和下拉菜单

#### 辅助工具

- 我们可以在JSX代码中直接检查一些可访问性特性。通常，JSX aware IDE中已经为ARIA角色、状态和属性提供了智能感知检查
- 常用辅助工具
  - [eslint-plugin-jsx-a11y](https://github.com/evcohen/eslint-plugin-jsx-a11y)
    - 提供了关于JSX中的可访问性问题的AST linting反馈。许多IDE允许您将这些发现直接集成到代码分析和源代码窗口中
  - [Create React App](https://github.com/facebookincubator/create-react-app)
    - 它激活了一些规则。如果想启用更多的可访问性规则，可以在项目的根目录中使用该内容创建`.eslintrc`文件

    ```jsx
    {
      "extends": ["react-app", "plugin:jsx-a11y/recommended"],
      "plugins": ["jsx-a11y"]
    }
    ```

#### 在浏览器中测试

- 有许多工具可以在浏览器的web页面上运行可访问性审计，请将它们与这里提到的其他易访问性检查结合使用，因为它们只能测试HTML的技术易访问性
- Deque Systems为应用程序的自动化和端到端可访问性测试提供了[aXe-core](https://github.com/dequelabs/axe-core)。这个模块包括Selenium的集成
- [aXe](https://www.deque.com/products/axe/)是一个基于axis内核的易访问性检查器浏览器扩展
- 还可以使用[react-axe](https://github.com/dylanb/react-axe)模块在开发和调试时直接向控制台报告这些可访问性结果
- [Web Accessibility Evaluation Tool](https://wave.webaim.org/extension/)是另一个易访问性浏览器扩展
- [可访问性树](https://www.paciellogroup.com/blog/2015/01/the-browser-accessibility-tree/)是DOM树的子集，它包含每个DOM元素的可访问对象，这些元素应该公开给辅助技术，比如屏幕阅读器
- 在某些浏览器中，我们可以很容易地查看可访问性树中每个元素的可访问性信息
  - [使用Firefox中的可访问性检查器](https://developer.mozilla.org/en-US/docs/Tools/Accessibility_inspector)
  - [激活Chrome中的可访问性检查器](https://gist.github.com/marcysutton/0a42f815878c159517a55e6652e3b23a)
  - [在OS X Safari中使用可访问性检查器](https://developer.apple.com/library/content/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)

#### 屏幕阅读器

- 使用屏幕阅读器进行测试应该成为易访问性测试的一部分
- 请注意浏览器/屏幕阅读器的组合很重要。建议在最适合自己选择的屏幕阅读器的浏览器中测试自己的应用程序
- NVDA in Firefox
  - [NonVisual Desktop Access](https://www.nvaccess.org/)或NVDA是一种广泛使用的开源Windows屏幕阅读器
    - 使用指南参考
      - [WebAIM - 使用NVDA来评估网页的可访问性](https://webaim.org/articles/nvda/)
      - [Deque - NVDA键盘快捷键](https://dequeuniversity.com/screenreaders/nvda-keyboard-shortcuts)
- VoiceOver in Safari
  - VoiceOver是苹果设备上的一个集成屏幕阅读器
    - 使用指南参考
      - [WebAIM - 使用VoiceOver来评估网站的可访问性](https://webaim.org/articles/voiceover/)
      - [Deque - 作为OS X键盘快捷键的VoiceOver](https://dequeuniversity.com/screenreaders/voiceover-keyboard-shortcuts)
      - [Deque - 作为iOS快捷方式VoiceOver](https://dequeuniversity.com/screenreaders/voiceover-ios-shortcuts)
- JAWS in Internet Explorer
  - [Job Access With Speech](https://www.freedomscientific.com/Products/software/JAWS/)是一个大量使用的屏幕上的窗口阅读器
    - 使用指南
      - [WebAIM - 使用JAWS评估Web可访问性](https://webaim.org/articles/jaws/)
      - [Deque - JAWS键盘快捷键](https://dequeuniversity.com/screenreaders/jaws-keyboard-shortcuts)
- ChromeVox in Google Chrome
  - [ChromeVox](https://www.chromevox.com/)是Chromebooks上的一个集成屏幕阅读器，可以作为谷歌Chrome的扩展
    - 使用指南
      - [Google Chromebook Help - 使用内置的屏幕阅读器](https://support.google.com/chromebook/answer/7031755?hl=en)
      - [ChromeVox经典键盘快捷键参考](https://www.chromevox.com/keyboard_shortcuts.html)

## 代码打包和拆分

- [Webpack](https://webpack.js.org/) or [Browserify](http://browserify.org/)
- 拆分方面[Code-Splitting](https://webpack.js.org/guides/code-splitting/)和[factor-bundle](https://github.com/browserify/factor-bundle)

### 动态import

- webpack和next.js自动支持这种语法
- 在使用Babel时，需要确保Babel能够解析动态导入语法，但不会转换它。为此，需要[babel-plugin-syntax-dynamic-import](https://yarnpkg.com/en/package/babel-plugin-syntax-dynamic-import)

```jsx
// before
import { add } from './math';

console.log(add(16, 26));

//after ？？？？
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

### React.lazy

- React.lazy 和 Suspense 还不适合做服务端渲染，如果需要给服务端渲染的APP做代码分割，推荐[Loadable Components](https://github.com/smooth-code/loadable-components),使用指南[guide for bundle splitting with server-side rendering](https://github.com/smooth-code/loadable-components/blob/master/packages/server/README.md)
- React.lazy函数允许将动态导入渲染为常规组件
- React.lazy takes a function that must call a dynamic import(). This must return a Promise which resolves to a module with a default export containing a React component

```jsx
// before
import OtherComponent from './OtherComponent';

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}

// after
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}
// 这将在渲染此组件时自动加载包含其他组件的包
```

- 如果包含其他组件的模块在MyComponent渲染时还没有加载，那么我们必须在等待加载时显示一些提示内容，比如加载指示器。这是使用Suspense组件完成的
- fallback属性的值可以使任意react元素

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

- Suspense组件可以放在lazy component外面的任意层位置，也可以用一个Suspense组件包裹多个lazy component

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```

- Error boundaries(错误边界)
- 如果另一个模块加载失败(例如，由于网络故障)，它将触发一个错误。我们可以处理这些错误，以显示良好的用户体验，并使用错误边界管理恢复。一旦创建了错误边界，就可以使用它在延迟组件之上的任何位置显示网络错误时的错误状态

```jsx
import MyErrorBoundary from './MyErrorBoundary';
const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

- Route-based code splitting(基于路由的代码分割)
- [React Router](https://reacttraining.com/react-router/) with React.lazy

```jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import React, { Suspense, lazy } from 'react';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

- Named Exports
- React.lazy目前只支持default exports,如果想要import时导入named exports,我们可以创建中间模块，重新导出为default exports，这还能确保自己不会引入无用的组件

```jsx
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;

// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";

// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```

## context 上下文 (类似于广播)

- 避免多层传递props需要中间组件
- 只有当上下文是被处于不同嵌套等级的多个组件使用时才使用context,否则尽量不要使用上下文，因为它使得组件复用更加困难
- 如果只是想要避免多层嵌套等级传值，可以参考组件间的合成(component composition)

```jsx
// 创建一个上下文对象ThemeContext，它的defaultValue为 'light'
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // 使用Provider来传递当前value值到组件树，任何组件树里的组件，都能狗读取数据
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间组件不需要传递value值
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 定义一个contextType来读取上下文对象，react将会找到往外数最近的上下文Provider，并使用它的value
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

- 不使用上下文来解决传值问题

```jsx
// 多层传值
<Page user={user} avatarSize={avatarSize} />
// ... which renders ...
<PageLayout user={user} avatarSize={avatarSize} />
// ... which renders ...
<NavigationBar user={user} avatarSize={avatarSize} />
// ... which renders ...
<Link href={user.permalink}>
  <Avatar user={user} size={avatarSize} />
</Link>
```

```jsx
// 直接将传好值的组件userLink通过属性一层层往下传
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// Now, we have:
<Page user={user} avatarSize={avatarSize} />
// ... which renders ...
<PageLayout userLink={...} />
// ... which renders ...
<NavigationBar userLink={...} />
// ... which renders ...
{props.userLink}
```

```jsx
// 传递多个组件
function Page(props) {
  const user = props.user;
  const content = <Feed user={user} />;
  const topBar = (
    <NavigationBar>
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    </NavigationBar>
  );
  return (
    <PageLayout
      topBar={topBar}
      content={content}
    />
  );
}
```

## API

### React.createContext

- `const MyContext = React.createContext(defaultValue);`
- 创建上下文对象。当React渲染绑定此上下文对象的组件时，它将往外从树中与其最近的Provider读取当前上下文值
- defaultValue参数仅当组件在树中往外没有匹配的Provider时才使用。这有助于在不封装组件的情况下独立测试组件。注意:传递undefined的Provider值并不会导致使用组件使用defaultValue

### Context.Provider

- `<MyContext.Provider value={/* some value */}>`
- 每个上下文对象都来自于一个Provider React component，它允许使用中的组件绑定上下文
- 接收一个名为`value`的prop,用来传递给所有使用中的子代组件。一个Provider能关联多个组件
- 一旦Provider的`value`更新，所有的子代组件都重新渲染。Provider与它子代组件之间的数据传播不受`shouldComponentUpdate`方法的限制，因此即使祖先组件退出更新，使用者也会被更新
- 更改是通过使用与[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)相同的算法比较新值和旧值来确定的
  - 在将对象作为值传递时，确定更改的方法可能会导致一些问题,详细请查看[Caveats](https://reactjs.org/docs/context.html#caveats)

### Class.contextType

- class类里的`contextType`属性用来接收由`React.createContext()`创建的上下文对象，通过`this.context`调用这个上下文的value值，并且可以在所有的声明周期方法里引用这个值
- 通过这个API只能绑定单个上下文

```jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* perform a side-effect at mount using the value of MyContext */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* render something based on the value of MyContext */
  }
}
const MyContext = React.createContext(
  themes.dark // default value
);
MyClass.contextType = MyContext;
```

- 如果使用实验性的[public class fields syntax](https://babeljs.io/docs/plugins/transform-class-properties/)，可以使用`static`字段初始化contextType

```jsx
class MyClass extends React.Component {
  static contextType = MyContext;
  render() {
    let value = this.context;
    /* render something based on the value */
  }
}
```

### Context.Consumer

- 绑定上下文变化的一个React组件，这让我们可以绑定一个上下文到函数组件里
- 接收一个函数作为子组件，这个函数接收当前上下文的值并返回一个React节点，传递给函数的`value`参数相当于最近的上下文的Provider的`value`,如果找不到Provider，就默认使用上下文创建时的`defaultValue`

```jsx
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```

### 从嵌套组件里更新上下文

```jsx
// theme-context.js
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});

// theme-toggler-button.js
import {ThemeContext} from './theme-context';

function ThemeTogglerButton() {
  return (
    <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (
        <button
          onClick={toggleTheme}
          style={{backgroundColor: theme.background}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

export default ThemeTogglerButton;

//app.js
import {ThemeContext, themes} from './theme-context';
import ThemeTogglerButton from './theme-toggler-button';

class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme,
    };
  }

  render() {
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}

function Content() {
  return (
    <div>
      <ThemeTogglerButton />
    </div>
  );
}

ReactDOM.render(<App />, document.root);
```

### 动态上下文

```jsx
// theme-context.js 设置themes对象和ThemeContext上下文对象
export const themes = {
  light: {
    foreground: '#000000',
    background: '#eeeeee',
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222',
  },
};

export const ThemeContext = React.createContext(
  themes.dark // default value
);

//themed-button.js
import {ThemeContext} from './theme-context';

class ThemedButton extends React.Component {
  render() {
    let props = this.props;
    let theme = this.context;
    return (
      <button
        // 获取父元素所有的属性
        {...props}
        style={{backgroundColor: theme.background}}
      />
    );
  }
}
ThemedButton.contextType = ThemeContext;

export default ThemedButton;

//app.js
import {ThemeContext, themes} from './theme-context';
import ThemedButton from './themed-button';

function Toolbar(props) {
  return (
    <ThemedButton onClick={props.changeTheme}>
      Change Theme
    </ThemedButton>
  );
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light,
    };

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };
  }

  render() {
    // 在ThemeProvider内的ThemedButto使用state里的theme,外面的ThemedButto使用上下文默认的theme
    return (
      <Page>
        <ThemeContext.Provider value={this.state.theme}>
          <Toolbar changeTheme={this.toggleTheme} />
        </ThemeContext.Provider>
        <Section>
          <ThemedButton />
        </Section>
      </Page>
    );
  }
}

ReactDOM.render(<App />, document.root);
```

### Consuming Multiple Contexts

- 为了保持上下文的快速重新渲染，React需要使每个上下文使用者成为树中的一个单独节点 ？？？？
- 如果经常同时使用两个或多个上下文值，我们可能需要考虑创建自己的render prop组件，该组件同时提供这两个值 ？？？？

```jsx
const ThemeContext = React.createContext('light');
const UserContext = React.createContext({
  name: 'Guest',
});

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // App component that provides initial context values
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

// A component may consume multiple contexts
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

### 注意

- 因为上下文使用引用标识来确定何时重新渲染，所以当provider的父类重新呈现时，可能会触发consumers中的一些意外渲染。例如，下面的代码将在每次提供者重新呈现时重新呈现所有消费者，因为总是为值创建一个新对象

```jsx
// 每次Provider重新渲染时重新渲染所有consumers，因为总是为`value`创建一个新对象
class App extends React.Component {
  render() {
    return (
      <Provider value={{something: 'something'}}>
        <Toolbar />
      </Provider>
    );
  }
}
```

```jsx
// 解决办法就是把value值提升到Provider的父类的state里
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: {something: 'something'},
    };
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```

### [未来可能移除的API](https://reactjs.org/docs/legacy-context.html)

## Error Boundaries

- 在用户界面层某部分发生的JS错误不应导致整个APP瘫痪
- 错误边界指的是一种React组件，它在其子组件树中的任何位置捕获JavaScript错误，记录这些错误，并显示一个备用UI，而不是崩溃的组件树。错误边界捕获渲染过程中的错误，生命周期方法中的错误，以及它们下面整个树的构造函数中的错误
- 错误边界无法捕捉的错误
  - 事件处理
  - 异步代码（比如`setTimeout` or `requestAnimationFrame` callbacks）
  - 服务端渲染
  - 错误抛出的地方是错误边界组件自己而不是子组件
- 一个错误边界组件首先必须是一个class组件，需要定义生命周期方法`static getDerivedStateFromError()` 和 `componentDidCatch()`
  - 错误抛出后，使用`static getDerivedStateFromError()`去渲染一个备用UI
  - 使用`componentDidCatch()`去记录错误信息

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

function rightWidget() {
  return (
    <ErrorBoundary>
      <MyWidget />
    </ErrorBoundary>
  );
}
```

- [Live Demo](https://codepen.io/gaearon/pen/wqvxGa?editors=0010)
- 使用时机取决于自己的需要。可以封装顶级路由组件，以便向用户显示“出了什么问题”消息，就像服务器端框架经常处理崩溃一样。还可以在错误边界中包装单个小部件，以防止它们破坏应用程序的其他部分
- 从React 16开始，没有被任何错误边界捕获的错误将导致卸载整个React组件树,因为保留损坏的UI比完全删除它更糟糕，极有可能显示错误的信息
- React会在控制台打印错误消息、JavaScript堆栈之外、组件堆栈跟踪提示信息
- [Create React App](https://github.com/facebookincubator/create-react-app)项目默认在组件堆栈跟踪时同时显示文件名及行号，如果没有使用`Create React App`,也可以手动添加[plugin](https://www.npmjs.com/package/babel-plugin-transform-react-jsx-source)到Babel的config,但这只能在开发环境下使用
- 显示在堆栈跟踪中的组件名是基于[Function.name](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name)属性，如果app需要支持老版浏览器或者设备，他们可能不会自动支持。这时可以考虑在打包应用程序中引入一个`Function.name`polyfill，比如[function.name-polyfill](https://github.com/JamesMGreene/Function.name),相对地，我们需要明所有组件中明确地设置[displayName](https://reactjs.org/docs/react-component.html#displayname)属性
- try/catch语句只针对命令式代码生效,而React组件是陈述性的，它指定什么需要被渲染
- React不需要错误边界来揭示事件处理程序中的错误。与render方法和生命周期方法不同，事件处理程序不会在渲染期间发生。所以如果他们抛出，React仍然知道在屏幕上显示什么
- 如果需要捕获一个在事件处理中的错误，就可以对statement使用`try/catch`

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { error: null };
    this.handleClick = this.handleClick.bind(this);
    this.showButton = this.showButton.bind(this);
  }

  handleClick() {
    try {
      // Do something that could throw
      this.showButton();
    } catch (error) {
      this.setState({ error });
    }
  }
  showButton() {
    ...
  }

  render() {
    if (this.state.error) {
      return <h1>Caught an error.</h1>
    }
    return <div onClick={this.handleClick}>Click Me</div>
  }
}
```

- React 15 不支持 error boundaries，而是要用`unstable_handleError`,所以升级为16时，要把它改成`componentDidCatch`