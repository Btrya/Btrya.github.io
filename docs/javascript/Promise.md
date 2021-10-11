## Promise A+ 规范

### 术语
1. promise 有 then 方法的对象或者函数
2. thenable 是一个有 then 方法的对象或者是函数
3. value promise状态成功时的值， `resolve(value)` ,这个value的值可以是任意类型： stirng number boolean undefined thenable promise
4. reason promise状态失败时的值， `reject(reason)`
5. exception 使用thorw抛出的异常

### 规范

#### Promise States
有三种状态，他们之间的流转关系

1. pending
   1.1 初始状态，可改变
   1.2 promise在resolve和reject前都是处于这个状态
   1.3 通过 resolve -> fulfilled 状态
   1.4 通过 reject -> rejected状态
2. fulfilled
   2.1 最终态，不可变
   2.2 一个 promise 被 resolve之后会变成这个状态
   2.3 必须拥有一个value的值 (resolve() 会resolve一个undefined)
3. rejected
   3.1 最终态，不可变
   3.2 一个 promise 被reject 之后会变成这个状态
   3。3 必须拥有一个reason的值不

总结：         |-> resolve(value) -> fulfilled状态
    pending --
              |-> reject(reason) -> rejected状态

#### then
promise 应该提供一个 then 方法，用来访问最终的结果，无论value 还是reason.

```js
promise.then(onFulfilled, onRejected)
```

1. 参数规范
  - onFulfilled 必须是函数类型，如果传入的不是函数，应该被忽略
  - onRejected 必须是函数类型，如果传入的不是函数，应该被忽略
2. onFulfilled特性
  - 在pormise变成fulfilled时，应该调用onFulfilled，参数是value (onFulfilled的执行时机？)
  - 在promise变成fulfilled之前，不应该调用 onFulfilled
  - 只能被调用一次 （怎么实现只调用一次？）
3. onRejected特性
  - 在promise变成 rejected时，调用 onRejected，参数是reason
  - 在promise变成 rejected之前，不应该调用 onRejected
  - 只能被调用一次
4. onFulfilled 和 onRejected 应该是在微任务阶段执行
  实现promise的时候，如何去生成微任务。
5. then 方法可以被调用多次
  - promise 状态变成 fulfilled后，所有的 onFulfilled 回调都需要按照注册的顺序执行，也可以理解为按照.then的顺序执行
  - promise 状态变成 rejected后，所有的 onRejected 回调都需要按照注册的顺序执行，也可以理解为按照.then的顺序执行
6. 返回值
  then 应该返回一个 promise
  ```js
  const promise2 = promise.then(onFulfilled, onRejected)
  ```
  - onFulfilled 或 onRejected 执行结果为 x，调用 resolvePromise 
  - onFulfilled 或 onRejected 执行过程中抛出了异常e，promise2需要被reject
  - 如果 onFulfilled 不是一个函数， promise2 以 pormise1 的 value 触发 fulfilled
  - 如果 onRejected 不是一个函数， promise2 以 pormise1 的 reason 触发 rejected
7. resolvePromise

  ```js
    resolvePromise(promise2, x, resolve, reject)
  ```
  - 如果promise2和x相等，reject TypeError
  - 如果x是一个promise
    如果x是pending，promise必须要在pending状态，直到x的状态改变
    如果x是 fulfilled, value -> fulfilled
    如果x是 rejected, reason -> rejected
  - 如果 x 是一个Object / Function
    去获取 x.then, 如果报错 reject e (可能修改了原型链)
    then 是一个函数， then.call(x, resolvePromiseFn, rejectPromiseFn)

    resolvePromiseFn 的入参是y,执行resolvePromise(newPromise, y, resolve, reject)
    如果调用then的时候抛出了异常e, reject reaon

## 实现 Promise

1. `const promise = new Promise();`  代表 Promise 应该是一个构造函数或者 class。
2. 定义三种状态
3. 初始化状态 -> pendin，初始化value和reason为null
4. resolve 和 reject 方法
   4.1 这两个方法要更改 status , 从 pending 变成 fulfilled / rejected
   4.2 入参 分别是 value / reason
5. 对于实例化promise时的入参处理
   5.1 入参是一个函数，接收 resolve reject 两个参数
   5.2 初始化promise的时候，就要同步执行这个函数，并且有任何的报错都要通过reject抛出去
6. then 方法
   6.1 then 接收两个参数： onFulfilled 和 onRejected 
   6.2 检查并处理参数，如果参数不是函数，则忽略
   6.3 根据当前promise的状态，调用不同的函数
   6.4 首先我们要拿到所有的回调，新建两个数组，分别存储成功和失败的回调，调用then的时候，如果还是pending就存入数组
   6.5 在 status 发生变化的时候，执行回调，用到getter setter，监听status的变化。在发生变化的时候来做对应的操作
7. then的返回值
   7.1 如果 onFulfilled 或者 onRejected 抛出一个异常e，那么新的promise必须reject e
   7.2 返回值应该是一个 promise
   7.3 如果 onFulfilled 不是函数，且promise1成功执行，那么promise2必须返回同样的状态和value
   7.4 如果 onRejected 不是函数，且promise1拒绝执行，那么promise2必须返回同样的状态和reason
   7.5 如果 onFulfilled 或者 onRejected 返回一个值 x , 运行 resolvePromise 方法。
8. resolvePromise
9. onFulfilled 和 onRejected 在微任务中运行


### 问题

1. promise的参数，执行时机是什么时候？  
2. status 有哪些？他们的状态流转是怎么样的？
3. 回调，onFulfilled 和 onRejected 的回调，分别的执行时机是什么？
4. 静态方法和实例方法有什么区别？
5. generator 特性是什么？如果遍历一个数组，只调用一次next，会输出所有值吗？
6. async await规范？


## 微任务与宏任务
js在异步模式，异步任务主要分为宏任务和微任务两种，宏任务称为 Task , 微任务称为 Jobs。
宏任务主要由宿主(浏览器，node)发起的，微任务由 JS 自身发起

### 宏任务创建方式
- setTimeout
- setInterval
- MessageChannel
- I/O,事件队列
- setImmediate(Node环境)
- script(整体代码块)

### 微任务创建方式
- requestAnimationFrame(有争议)
- MutaionObserver(浏览器环境)
- Promise.[then/catch/finally]
- process.nextTick(node环境)
- queueMicrotask

### 如何理解  script（整体代码块）是个宏任务?
如果同时存在两个 srcipt 代码块，会首先在执行第一个 srcipt 代码块中的同步代码，如果这个过程中创建了微任务并进入了微任务队列，第一个script同步代码执行完之后，会首先去清空微任务队列，再去开启第二个 script 代码块的执行。

### 什么是 EvnetLoop ？
![Image text](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2baaf009636748c491898aafeceddb32~tplv-k3u1fbpfcp-watermark.image)
1. 判断宏任务队列是否为空
- 不为空 -> 执行最早进入队列的任务 -> 执行下一步
- 空 -> 执行下一步

2. 判断微任务队列是否为空
- 不为空 -> 执行最早进入队列的任务 -> 继续检查微任务队列是否为空
- 空 -> 执行下一步

例题
```js
let promise = new Promise((resolve, reject) => {
  console.log(2)  // 同步2

  queueMicrotask(() => { // 微任务a
    console.log(3) // 同步a1
    setTimeout(() => { // 微任务中的宏任务aa
      console.log(4)
    }, 0)
    console.log(5) // 同步a2
    resolve()
  })

  setTimeout(() => { // 宏任务b
    console.log(6)
  }, 0)

  console.log(7) // 同步7
})
console.log(8) // 同步8
promise.then(res => { // 微任务c
  console.log(9)
})


// 27835964
```

## Generator 和 Async 简介

### 协程
`async/await`是一个自动执行的 `Generator` 函数

`协程是一种比线程更加轻量级的存在。`
可以把协程看成是跑在线程伤的任务，`一个任务可以存在多个协程，但是在线程上同时只能执行一个协程。`
比如当前执行的是 A 协程，要启动 B 协程，那么 A 协程就需要将主线程的控制权交给 B 协程，这就体现在 A 协程暂停执行， B 协程恢复执行; 同样，也可以从 B 协程中启动 A 协程。通常，`如果从 A 协程启动 B 协程，我们就把 A 协程称为 B 协程的父协程。`

正如一个进程可以拥有多个线程一样，一个线程也可以拥有多个协程。最重要的是，协程不是被操作系统内核所管理，而是完全`由程序所控制`（即在用户态执行）。这样带来的好处就是性能得到了很大的提升，不会像线程切换那样消耗资源。

### 协程的四点规则
- 通过调用生成器函数 genDemo 来创建一个协程 gen , 创建之后, gen 协程并没有立即执行
- 要让 gen 协程执行，需要通过调用 gen.next
- 当协程正在执行的时候, 可以通过 yield 关键字来暂停 gen 协程的执行，并返回主要信息给父协程
- 如果协程在执行期间，遇到了 return 关键字，那么 JS 引擎会结束当前协程，并将 return 后面的内容返回给父协程

### 协程之间的切换
- gen 协程和父协程是在主线程上交互执行的，并不是并发执行的，它们之间的切换是通过 yield 和 gen.next 来配合完成的
- 挡在 gen 协程中调用了 yield 方法时， JS 引擎会保护 gen 协程当前的调用栈信息，并恢复父协程的调用栈信息。同样，当在父协程中执行 gen.next 时， JS 引擎会保存父协程的调用栈信息，并恢复 gen 协程的调用栈信息。


### 迭代器 Iterator

迭代器 Iterator 是ES6 引入的一个钟新的遍历机制，同时也是一种特殊对象，它具有一些专门为迭代过程设计的专有接口。

每个迭代器对象都有一个next()方法，每次调用都返回一个当前结果对象，当前结果对象中有两个属性：

1. value：当前属性的值
2. done： 用于判断是否遍历结束，当没有更多可返回的数据时，返回true

每调用一次next()方法，都会返回下一个可用的值，直到遍历结束。

### 生成器 Generator

生成器是一种返回迭代器的函数，通过functuion关键字后的星号(*)来表示，函数中会用到新的关键字yield，星号可以紧挨着function关键字，也可以在中间添加一个空格。

```js
function generator() {
  const list = [1, 2, 3]
  for (let i of list) {
    yield i;
  }
}

let g = generator()

console.log(g.next()) // {value: 1, done: false}
console.log(g.next()) // {value: 2, done: false}
console.log(g.next()) // {value: 3, done: false}
console.log(g.next()) // {value: undefined, done: true}
```

### 特性

1. 每当执行完一条yield语句后函数就会自动停止执行，直到再次调用next();
2. yield关键字只可在生成器内部使用，在其他地方使用会导致程序抛出错误;
3. 可以通过函数表达式来创建生成器，但是不能使用尖头函数
   `let generator = function *() {}`

### Async 和 Await

```js
function longTimeFn(time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(time)
    }, time)
  })
}

async function test() {
  const value = await longTimeFn(2000)
  console.log(value) // 2000
}

test()
```

### async await 和 promise

都是为了解决回调地狱

ES7引入 async await 只是相当于把 promise 变成一种同步的写法, `在不阻塞主线程的情况下使用同步代码实现异步访问资源的能力`

其实 async await 的秘密就是 Promise 和 Generator 生成器应用，往低层说就是 微任务和协程应用。

#### async
根据 MDN 定义, async 是一个通过 `异步执行并隐式返回 Promise` 作为结果的函数
```js
aysnc function async1() {
  return '123'
}
console.log(async1()) // Promise {<fulfilled>: '123'}
```

#### await
```js
async function foo() {
  console.log(1)
  let a = await 100
  console.log(a)
  console.log(2)
}
console.log(0)
foo()
console.log(3)

// 0 1 3 100 2
```