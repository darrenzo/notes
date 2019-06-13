# 异步编程

## 阻塞和非阻塞

- 阻塞和非阻塞描述的是一个操作对转移 CPU 控制权的影响
- 按现代 CPU 的设计，一个普通指令执行结束前，不会释放 CPU 控制权。而在编程语言中，一条语句执行结束前，不会执行下一条语句。而如果这种现象持续的时间相对较长，那么就称之为**阻塞**，因为它长时间地阻碍了后面的语句的执行
- 如果一个方法、接口、函数、过程内部需要执行一个较长时间的操作，迟迟不返回结果，那么这个接口我们称之为**阻塞**的。而如果一个接口会启动一个复杂的操作，但是不会立即返回结果，也不会长时间地阻碍后面语句的执行，那么我们称之为**非阻塞**的

## 同步 & 异步

- 同步和异步是两种编程方式，它们描述的是对操作结果的处理方式
- 简单地说
  - 如果一个函数在调用结束后立即就能拿到结果，那么这个函数我们称之为**同步**的
  - 如果一个函数在调用结束后拿不到结果，而必须通过别的手段才能拿到，那么这个函数是**异步**的

## js运行环境

- js的代码不是直接由CPU执行的，而是由解释器解释执行的
- 解释器本身只是用于执行JS代码，而要运行js程序，还需要比如标准库、代码加载器等等，所有这些东西合在一起就是一个运行环境，即虚拟机

## 单线程模型

- js代码运行在单线程上，在虚拟机内部，维护着一个事件循环机制，这个机制用于将时间片按计划执行
- 虚拟机本身是多线程的，JS代码全部在唯一的主线程上执行，其他的IO操作等则交给其他线程去执行

## 观察者模式

- 观察者模式是设计模式的一种，它被设计用于观察一个可观察对象内部状态的改变，以便在对象的外部对这些‘改变’进行相应的处理
- 所有的消息机制、事件机制都是观察者模式的实现

```js
class ObserverPattern {

    constructor() {
        // 初始化钩子集合对象
        this._hooks = {};
    }

    addListener(event, callback) {

        if (!this._hooks[event]) {

            this._hooks[event] = [];
        }

        // 新增需要监听的钩子
        this._hooks[event].push(callback);
    }

    trigger(event, ...args) {

        if (this._hooks[event]) {
            // 如果注册了这个钩子，则触发此钩子下所有的方法
            for (let callback of this._hooks[event]) {
                // 绑定所有钩子方法的this为实例对象，并执行钩子方法
                callback.apply(this, args);
            }
        }
    }
}

class Person extends ObserverPattern {

    constructor(name) {

        super();
        this.name = name;
    }

    sayHello() {

        console.log(`${this.name}: Hello!`);
        this.trigger("after_hello", Date.now());
    }
}

let test = new Person("Mick");

// 给对应的钩子添加方法
test.addListener("after_hello", function(date) {

    console.debug(`[DEBUG] ${this.name} said hello at ${new Date(date).toISOString()}`);
});

test.sayHello();
```

## 串行执行

### 同步版本

```js
const products = {

    "1": {
        "price": 3.44
    },
    "2": {
        "price": 5.55
    }
};

const cart = [{

    "product_id": 1,
    "quantity": 3

},{

    "product_id": 2,
    "quantity": 2

}];

function getProductPriceById(id) {

    return products[id].price;
}

function getCart() {

    return [...cart];
}

console.log(
    'Total:',
    getCart().reduce(
        (p, v) => p + getProductPriceById(v.product_id) * v.quantity,
        0
    )
);
```

### 嵌套式异步版本

### 平坦式异步版本

```js

function _seriesSimplifiedCall(final, fns) {

    // 每次取出串行方法集里的第一个
    const fn = fns.shift();

    if (fn) {

        fn(
            // next()方法 --B
            function(err) {
                // 如果给next()方法传了参数（规定只有在报错时才给next()方法传递参数，此时传的参数就是err信息）
                if (err) {
                    // 如果报错，就直接执行final方法
                    final(err);
                }
                else {
                    // 如果没有报错，继续传递final方法和剩余的方法数组fns执行
                    _seriesSimplifiedCall(final, fns);
                }
            }
            // next()方法 --E
        );
    }
    else {

        final();
    }
}

function series(fns, final) {

    _seriesSimplifiedCall(final, fns.slice());
}

series([

    function(next) {
        // 如果报错，就给next()方法传递error信息参数
        if (error) {

            return next(error);
        }

        next();
    },

    // ...

    function(next) {

        if (error) {

            return next(error);
        }

        next();
    }

], function(error) {

    if (error) {

        handleError(error);
        return;
    }

    onSuccess();
});
```

#### 关联上下文式写法

```js
// 串行执行时，以参数形式传递上下文对象context，减少全局变量
function _series_call(final, fns, context) {

    const fn = fns.shift();

    if (fn) {

        fn(function(err) {

            if (err) {

                final(err, context);
            }
            else {

                _series_call(final, fns, context);
            }
        }, context);
    }
    else {

        final(null, context);
    }
}

function series(context, fns, final) {

    _series_call(final, fns.slice(), context);
}

// 第一个参数就是要传递的上下文对象
series({}, [

    function(next, ctx) {

        getCart(function(err, cartItems) {

            if (err) {

                return next(err);
            }

            ctx.cart = cartItems;
            next();
        });
    },

    function(next, ctx) {

        getProductById(ctx.cart[0].product_id, function(err, productInfo) {

            if (err) {

                return next(err);
            }

            ctx.product = productInfo;
            next();
        });
    }

], function(err, ctx) {

    if (err) {

        console.err(err);

        return;
    }

    console.log('1st Total:', ctx.product.price * ctx.cart[0].quantity);
});
```

## 串行迭代

### 同步式

```js
function getProductPriceById(id) {

    return products[id].price;
}

function getCart() {

    return [...cart];
}

let total = 0;

for (let x of getCart()) {

    total += getProductPriceById(x.product_id) * x.quantity;
}

console.log(`Total: ${total}`);
```

### 平坦式异步版本

```js
// 循环函数体
function _forEach(ctx, index, items, handler, final) {

    if (index < items.length) {
        // 循环的机制
        handler(items[index], index, ctx, function(err) {

            if (err) {

                final(err, ctx); // 这里finnal是_forEach()函数传的最后一个参数方法
            }
            else {

                _forEach(ctx, index + 1, items, handler, final);
            }
        });
    }
    else {

        final(null, ctx);
    }
}

function forEach(ctx, items, handler, final) {

    _forEach(ctx, 0, items.slice(0), handler, final);
}


series({}, [

    function (next, ctx) {

        getCart(function(err, cart) {

            if (err) {

                return next(err);
            }

            ctx.cart = cart;
            ctx.total = 0;

            next();
        });
    },

    function(next, ctx) {

        forEach(ctx, ctx.cart, function(item, index, ctx, next) {

            getProductById(item.id, function(err, product) {

                if (err) {

                    return next(err);
                }

                ctx.total += ctx.cart[index].quantity * product.price;

                next();
            });

        }, next); // 这个next()方法是series方法里的next()方法，如果forEach里有出现报错，则执行next(err, ctx),最后触发series()方法的final()方法
    }

], function(err, ctx) {

    if (err) {

        console.err(err);

        return;
    }

    console.log(`Total (in cart): ${ctx.total}.`);
});
```

## 并行操作

### 同步版本

- JavaScript 不支持多线程，同步阻塞接口不能并行执行。

### 异步版本

```js
function queryUserWallet(id, callback) {

    return ajaxGet(`/api/users/${id}/wallet`, callback);
}

function queryUserProfile(id, callback) {

    return ajaxGet(`/api/users/${id}/profile`, callback);
}

function queryUserArticles(id, callback) {

    return ajaxGet(`/api/users/${id}/articles`, callback);
}

queryUserArticles(1, function(err, result) {

});

queryUserProfile(1, function(err, result) {

});

queryUserWallet(1, function(err, result) {

});
```

### 异步简化版本

```js
function parallel(context, fns, final) {

    const errors = [];
    let completed = 0;

    for (let key in fns) {

        fns[key](function(error) {

            if (error) {

                errors.push({
                    "name": key,
                    "error": error
                });
            }

            completed++;

            if (completed === Object.keys(fns).length) {

                final(errors, context);
            }

        }, context);
    }
}

parallel({}, {

    articles: function(next, ctx) {

        queryUserArticles(1, function(err, result) {

            if (err) {

                return next(err);
            }

            ctx.articles = result;

            next();
        });
    },

    profile: function(next, ctx) {

        queryUserProfile(1, function(err, result) {

            if (err) {

                return next(err);
            }

            ctx.profile = result;

            next();
        });
    },

    wallet: function(next, ctx) {

        queryUserWallet(1, function(err, result) {

            if (err) {

                return next(err);
            }

            ctx.wallet = result;

            next();
        });
    }

}, function(errors, ctx) {

    if (errors.length > 0) {

        // handle error here.
        return;
    }
    // handle result here with context.
});
```

## 条件循环

### 同步版本

```js
let i = 0;

while (i++ < 10) {

    console.log(i);
}

console.log('done');
```

### 异步版本

```js
function loopIf(
    context,
    condition,
    loopBody,
    final
) {

    condition(function(result) {

        if (result) {

            loopBody(function(e) {

                if (e) {

                    final(e, context);
                }
                else {

                    loopIf(
                        context,
                        condition,
                        loopBody,
                        final
                    );
                }

            }, context);
        }
        else {

            final(null, context);
        }
    }, context);
}

loopIf(

    { cursor: 0 },
  
    function(confirm, ctx) {
  
        confirm(ctx.cursor++ < 10);
    },

    function(next, ctx) {
  
        console.log(ctx.cursor);
        // 具体的处理函数
        callInOneSecond(next);
    },
  
    function(error, context) {

        if (error) {

            console.error(error);
            return;
        }
  
        console.log('done', JSON.stringify(context, null, 2));
    }
);
```

## 任务队列

### 队列版迭代

```js
class AsyncTaskQueue {

    constructor() {

        this._tasks = [];

        this._running = false;
    }

    enqueue(...callbacks) {

        this._tasks.push(...callbacks);

        if (!this._running) {

            this._run();
        }
    }

    _run() {

        const callback = this._tasks.shift();

        if (!callback) {

            this._running = false;
            return;
        }

        this._running = true;

        callback(() => this._run());
    }
}

const queue = new AsyncTaskQueue();

let total = 0;

getCart(function(err, result) {

    queue.enqueue(...result.map((item) => function(next) {

        getProductById(item.product_id, function(err, product) {

            total += product.price * item.quantity;
        });
    }));

    queue.enqueue(function(next) {

        console.log("Total:", total);
        next();
    });
});
```

## 队列版本串行执行

```js
const queue = new AsyncTaskQueue();

let cart, product;

queue.enqueue(

    function(next) {

        getCart(function(err, cartItems) {

            if (err) {

                return next(err);
            }

            cart = cartItems;
            next();
        });
    },

    function(next) {

        getProductById(cart[0].product_id, function(err, productInfo) {

            if (err) {

                return next(err);
            }

            product = productInfo;
            next();
        });
    },

    function(next) {

        console.log('1st Total:', product.price * cart[0].quantity);

        next();
    }
);
```
