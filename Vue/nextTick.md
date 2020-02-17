## Vue 的更新策略

Vue **异步执行** DOM 更新。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 Watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后在下一个的事件循环 “tick” 中，Vue 刷新队列并执行实际（已去重）的工作。Vue 在内部对异步队列尝试使用原生的 **Promise.then**、**MutationObserver** 和 **setImmediate**，如果都不支持，则会采用 **setTimeout(fn, 0)** 代替。

所以要在数据变化后立即调用的话，可以使用 **Vue.nextTick(callback)** 回调来执行

## nextTick 到底是什么？

nextTick 其实做了两件事情，一是生成一个 timerFunc，把回调作为 microTask 或 macroTask 参与到事件循环中；二是把回调函数放入一个 callbacks 队列，等待适当时机执行

定义：在下次 DOM 更新循环结束后执行延迟回调。在修改数据后立即使用这个方法，获取更新后的 DOM

bug 警告：

在 Vue 2.4 之前都是使用的 **microtasks**，但是 microtasks 的优先级过高，在某些情况下可能会出现比事件冒泡更快的情况，但是如果都使用 **macrotasks** 又会出现渲染的性能问题。所以默认使用 microtasks，某些情况下使用 macrotasks，如 v-on。

不过在 Vue 2.5 中出现了怪异问题：safari 跳转到下一页再返回，正常应该是从 bfcache 中恢复页面，实际上却是页面回到顶部，js重新执行了，监听 pageshow 事件，event.persisted 是false。
原因是优先使用 MessageChannel 实现宏任务队列导致的 bug。

所以 Vue 2.6 又改回了全部使用 **microtasks**

### 执行实现

首先判断能否使用 Promise，不能的话使用 setImmediate，最后使用 setTimeout

原因：Promise 属于微任务，执行早于宏任务；另外 setTimeout 的最小时间延迟是 4ms，也就是说理想环境下异步回调最快的也是 4ms 后了，而 setImmediate 的延迟是明显小于 setTimeout 的；所以优先级 Promise > setImmediate > setTimeout

## 去除重复事件

事件是在 **queueWatcher** 队列中进行去重的，在获取到 Watcher 的 id 后，用 hash 表记录，如果之前出现过，则不会再次进入队列，然后通过 nextTick 异步执行 queueWathcer 进行视图的更新。