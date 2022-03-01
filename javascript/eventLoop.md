# Event Loop

## 为什么会有 Event Loop

- js的任务分为两种：
  - 同步：直接放在主线程上排队依次执行
  - 异步：放在任务队列中，若有多个异步任务则需要在任务队列中排队等待，任务队列类似于缓冲区，任务下一步会被移到调用栈然后主线程执行调用栈的任务
    - 调用栈是一个栈结构，函数调用会形成一个栈帧，帧中包含了当前执行函数的参数和局部变量等上下文信息，函数执行完后，它的执行上下文会从栈中弹出
- js 是单线程的，在ajax请求中，主线程在等待响应的过程中会去做其他事情，浏览器先在事件表注册ajax的回调函数，响应回来后回调函数被添加到任务队列中等待执行，不会造成线程阻塞，所以说js处理ajax请求的方式是异步的
  - 单线程是指js引擎中解析和执行js代码的线程只有一个（主线程），每次只能做一件事情
- 检查调用栈是否为空以及将某个任务添加到调用栈中的这个过程就是 event loop, 这是js实现异步的核心

## 浏览器中的 Event Loop

- Macro-Task(宏任务)
  - setTimeout、setInterval、script(整体代码)、I/O操作、UI渲染等
- Micro-Task(微任务)
  - process.nextTick （比promise优先级高），new Promise()的then、catch、finally回调，MutationObserve 等
- requestAnimationFrame
  - 它要执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行
  - 其也属于异步执行的方法，但该方法既不属于宏任务，也不属于微任务
  - 它是GUI渲染之前执行，但在Micro-Task之后，不过它不一定会在当前帧必须执行，由浏览器根据当前的策略自行决定在哪一帧执行
- 事件循环执行机制
  - 进入任务后挨个执行宏任务，接着执行微任务，此时遇到异步回调时，按时间顺序放入事件队列（同一时间只存在一个事件队列）
    - 执行宏任务或者微任务或事件队列时，优先按对应事件优先级顺序执行，优先级一样再按加入顺序执行
  - 先把当前宏任务中的微任务执行完毕，再在宏任务队伍列中执行下一个宏任务，宏任务的异步回调往宏任务队列中的最后追加

```js
console.log('1');
setTimeout(function () {
    console.log('2');
    process.nextTick(function () {
        console.log('3');
    })
    setTimeout(function () {
        console.log('4')
    })
    new Promise(function (resolve) {
        console.log('5');
        resolve();
    }).then(function () {
        console.log('6')
    })
})
new Promise(function (resolve) {
    console.log('7');
    resolve();
}).then(function () {
    console.log('8');
    new Promise(function (resolve) {
        console.log('9');
        resolve();
    }).then(function () {
      new Promise(function (resolve) {
          console.log('10');
          resolve();
      }).then(function () {
          console.log('11')
      })
    })
})

Promise.resolve(new Promise((resolve) => {
    console.log('12');
    resolve()
})).then(function() {
    console.log('13'); 
});

new Promise(function (resolve) {
    console.log('14');
    resolve();
}).then(function () {
    console.log('15');
    new Promise(function (resolve) {
        console.log('16');
        resolve();
    }).then(function () {
      new Promise(function (resolve) {
          console.log('17');
          resolve();
      }).then(function () {
          console.log('18')
          setTimeout(function () {
              console.log('24')
          })
      })
    })
})

process.nextTick(function () {
    console.log('19');
})

setTimeout(function () {
    console.log('20');
    process.nextTick(function () {
        console.log('21');
    })
    new Promise(function (resolve) {
        console.log('22');
        resolve();
    }).then(function () {
        console.log('23')
    })
})

// 1
// 7
// 12
// 14
// 19
// 8
// 9
// 13
// 15
// 16
// 10
// 17
// 11
// 18
// 2
// 5
// 3
// 6
// 20
// 22
// 21
// 23
// 24
// 4
```
