## what is

**Event Loop** 即时间循环，是指浏览器或者 Node 单线程运行时不会阻塞的一种机制，也就是我们经常使用异步的原理。

## Event Loop

在 JavaScript 中，任务被分为两种，一种宏任务(MacroTask),一种微任务(MicroTask)，都是队列。

- MacroTask：包含 setTimeout、setInterval、setImmediate(Node独有)、requestAnimationFrame(浏览器独有)、I/O、UI Rendering

- MicroTask：包含 Process.nextTick(Node 独有)、Promise、Object.observe、MutationObserver

## 浏览器中的Event Loop

JavaScript 有一个 **main thread** 主线程和 **call-stack** 调用栈

### 同步和异步任务

JavaScript 单线程任务被分为同步任务和异步任务，同步任务会在调用栈中按照顺序等待主线程依次执行，异步任务会在异步任务有了结果后，将注册的回调函数放入队列任务重等待主线程空闲的时候被读取到栈内等到主线程的执行。

### 执行流程

执行栈在执行完同步任务后，查看执行栈是否为空，如果执行栈为空，就会去检查微任务(microTask)队列是否为空，如果为空的话，就执行Task（宏任务），否则就一次性执行完所有微任务。
**每次单个宏任务执行完毕后，检查微任务(microTask)队列是否为空**，如果不为空的话，会按照先入先出的规则全部执行完微任务(microTask)后，设置微任务(microTask)队列为null，然后再执行宏任务，如此循环。

### 关于 async/await

```javascript
async function f() {
    await p()
    console.log('ok')
}
async function p() {
    console.log('p')
}
f()
new Promise((resolve) => {
    resolve()
}).then(() => {
    console.log('p1')
}).then(() => {
    console.log('p2')
})
```

#### 老版本Chrome

async / await 在底层转换成了 promise 和 then 回调函数，每次使用 await，解释器都会创建一个 promise 对象，然后把剩下的 async 函数中的操作放到 then 回调函数中，
即如果微任务中有其他then任务的话，会将当前的推迟时序（黑盒，不懂为啥）

输出结果：
> p -> p1 -> p2 -> ok

[ async await 和 promise微任务执行顺序问题 ](https://segmentfault.com/q/1010000016147496/)

```javascript
async function f() {
  await p
  console.log('ok')
}
// 转换为

function f() {
  return RESOLVE(p).then(() => {
    console.log('ok')
  })
}
```

#### 高版本Chrome

首先立即执行转换后 then 代码，然后执行其他代码

输出结果：
> p -> ok -> p1 -> p2

## Node 中的 Event Loop

Node 的Event Loop 中，执行宏任务共有6个阶段

- timers 阶段：执行 setTimeout 和 setInterval 预定的 callback
- I/Ocallback 阶段：执行除了 close 事件/timers/setImmediate 设定的 callback 之外的 callbacks
- idle,prepare 阶段，仅 node 内部使用
- poll 阶段： 获取新的 I/O 事件，适当条件下 node 将阻塞在这里
- check 阶段：执行 setImmediate() 设定的 callbacks
- close callbacks阶段：执行sockt.on("close",...) 这些 callbacks

在浏览器中，可以认为只有一个宏任务队列，所有的macrotask都会被加到这一个宏任务队列中，但是在NodeJS中，不同的macrotask会被放置在不同的宏队列中。

Node 的微任务队列

- next Tick Queue：用于放置 process.nextTick 的回调
- Other Micro Queue：用于放置其他微任务，如 Promise

在浏览器中，也可以认为只有一个微队列，所有的microtask都会被加到这一个微队列中，但是在NodeJS中，不同的microtask会被放置在不同的微队列中。

首先执行全局代码，然后执行 next Tick Queue 中的微任务，然后执行 Other Micro Queue 中的微任务，然后执行宏任务，每执行完一个阶段，才会去执行微任务（区别于浏览器），循环……

注意1：setTimeout 实际使用中可能会有小延时

注意2：node v11 与 node 10 有区别

注意3：宏任务会阻塞队列，如果读取一个文件需要 95ms，那么定义的 settimeout 的执行开始时间将会延后 95ms

现在node11在timer阶段的setTimeout,setInterval...和在check阶段的immediate都在node11里面都修改为一旦执行一个阶段里的一个任务就立刻执行微任务队列。


## 总结

1. 浏览器：只有一个宏任务和微任务队列，先执行全局script代码，等调用栈清空后，执行微任务队列；微任务清空后，执行宏任务队列的第一个任务，执行完后，继续执行微任务。注意和 Node 的区别，只取一个。
2. Node：首先执行全局代码，然后执行 next Tick Queue 中的微任务，然后执行 Other Micro Queue 中的微任务，然后执行宏任务，每执行完一个阶段，才会去执行微任务（区别于浏览器），循环……
3. MacroTask：包含 setTimeout、setInterval、setImmediate(Node独有)、requestAnimationFrame(浏览器独有)、I/O、UI Rendering
4. MicroTask：包含 Process.nextTick(Node 独有)、Promise、Object.observe、MutationObserver


[一次弄懂Event Loop](https://juejin.im/post/5c3d8956e51d4511dc72c200#heading-21)