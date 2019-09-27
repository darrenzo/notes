# es7

## async和await

### async 函数

- 这个函数总是返回一个promise，如果代码中有return <非promise>语句，JavaScript会自动把返回的这个value值包装成promise的resolved值

```js
async function f() {
    return 1
}
f().then(alert) // 1

async function a() {
    return Promise.resolve(1)
}
a().then(alert) // 1
```

### await

- 只能在async函数内部使用
- 关键词await可以让JavaScript进行等待，直到一个promise执行并返回它的结果，JavaScript才会继续往下执行
- await字面上使得JavaScript等待，直到promise处理完成，然后将结果继续下去。这并不会花费任何的cpu资源，因为引擎能够同时做其他工作：执行其他脚本，处理事件等等

```js
async function f() {
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve('done!'), 1000)
    })
    let result = await promise // 直到promise返回一个resolve值（*）
    alert(result) // 'done!'
}
f()
```

- await接受thenable对象（那些具有可调用的then方法的对象）
- 如果await得到了一个带有then方法的非promise对象，它将会调用提供原生函数resolve、reject作为参数的方法，然后await一直等待，直到他们其中的一个被调用

```js
class Thenable {
   constructor(num) {
       this.num = num
   }
   then(resolve, reject) {
       alert(resolve) // function() {native code}
       // 1000ms后将this.num*2作为resolve值
       setTimeout(()=> {resolve(this.num * 2), 1000})
   }
}
async function(f) {
   // 等待1s，result变为2
   let result = await new Thenable(1)
   alert(result)
}
f()
```

- class使用async方法

```js
class Waiter {
   async wait () {
       return await Promise.resolve(1)
   }
}
new Waiter().wait().then(alert) // 1
```

- 串行执行，就是await过程包括发送请求
- 并行执行，就是只await等待结果，而不要包括发送请求
  - 用立即执行函数包裹多个async函数，也可以达到并行执行的效果
  - await Promise.all()方法进行并行执行

### 错误处理

- 如果一个promise正常resolve，那么await返回这个结果，但是在reject的情况下会抛出一个错误，就好像在那一行有一个throw语句一样

```js
async function f() {
    await Promise.reject(new Error('whoops!'))
}

// 相当于
async function f() {
    throw new Error('Whoops!')
}
```

- promise在reject抛出错误之前可能需要一段时间，所以await将会等待，然后才抛出一个错误。我们可以使用try-catch语句捕获错误，就像在正常抛出中处理异常一样

```js
async function f() {
    try {
        let response = await fetch('/no-user-here')
        let user = await response.json()
    } catch(err) {
        // 在fetch和response.json中都能捕获错误
        alert(err)
    }
}
f()
```

- 如果我们不使用try-catch，然后async函数f()的调用产生的promise变成reject状态的话，我们可以添加.catch去处理它

```js
let response = await fetch('http://no-such-url').catch( () => { } );

// 或者

async function f() {
    let response = await fetch('http://no-such-url')
}
// f()变成了一个rejected的promise
f().catch(alert) // TypeError: failed to fetch
```

- 包装promise，使其返回统一的格式的代码

```js
function to (promise) {
  return promise.then(res => [null, res]).catch(err => [err])
}
const [err, res] = await to(fetchUser(true))
if (err) {
  console.error('touser err:', err)
}
```

## 串行和并行

```js
function fetchName () {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve('lujs')
      }, 3000)
    })
}

function fetchAvatar () {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve('123')
      }, 4000)
    })
}

// 串行
async fetchUser () {
    const name = await fetchName()
    const avatar = await fetchAvatar()
    return {
      name,
      avatar
    }
}

// 并行
async fetchUserParallel () {
    // 先生成实例
    const namePromise = fetchName()
    const avatarPromise = fetchAvatar()
    return {
      name: await namePromise,
      avatar: await avatarPromise
    }
}

// Promise.all并发请求
(async ()=>{
    let result = await Promise.all([getNafetchNameme(), fetchAvatar()]); // result 为两个Promise resolve 出的值组成的数组
})()
```
