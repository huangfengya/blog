## computed 具体流程

1. 初始化 computed 属性，声明一个 watchers 对象(空 object)并挂载到 vm 实例上，给所有的 computed 对象中的方法添加 Watcher 并放入 watchers；如果该方法是个函数，那么将其作为 Watcher 的 getter，如果是个对象，将其 get 函数作为其 getter；
2. Watcher 添加时相对与其他 watcher 多了一个 **option:{lazy: true}**，这个就是 **lazy Watcher** 的关键；置 watcher.dirty = lazy;
3. 如果 computed 的属性没有挂载到 vm 上，则通过 defineComputed 函数挂载（computed 不能和 data 重名的原因）；defineComputed 中，非服务端渲染会进行数据的缓存，其中 getter 设置为 createComputed(key) 的返回值，当你访问这个计算属性时就会调用getter。
4. 再看 **createComputedGetter** 这个函数返回了一个函数 **computedGetter**，在执行 getter 的时候，会判断该 Watcher.dirty 的值，为 true 则执行 Watcher.evaluate 方法，这儿会执行 Watcher.get 方法，实际上就是执行了上面用户定义的 getter，并将 watcher.dirty 置为 false；并在同时进行依赖的收集；如果 watcher.dirty 为 false ，则不会执行 watcher.evaluate 的重新计算，每次都会返回之前缓存的值。
5. 在 computed 所依赖的值改变后，调用 dep.notify()，会将computed 的 watcher.dirty 重新置为 true；另外由于 data 的改变会引起 template 的重新渲染，所以会重新读取 computed 的值，并触发 getter
6. 关于 computed 依赖 computed 个人理解是只能通过 data 的中间变量，因为 computed 是没有 setter 的，自然也没有办法触发 dep.notify()

## 如何记录依赖的收集

Dep.target 指的是自身，在初始化 computed 的时候会首先执行 watcher.evaluate，也就是第一次计算值，会调用 Watcher.get()，内部逻辑有设置 Dep.target 为自身，因为要读取其所依赖的值，所以会调用被依赖的 getter，被依赖 getter 里面有判断，如果 Dep.target 存在，说明有值依赖于自己，就把这个值添加进dep中，当被依赖这有变化时，通知期dep中的所有依赖者执行 update


## 简单来说

1. 初始化 computed 计算属性，如果是函数，则将其作为 watcher 的 getter，如果是对象，将 obj.get 作为 getter
2. 将 computed 挂载到 vm 实例上，首次进行计算，并进行依赖的收集，计算后将 Watcher.dirty 置为 false，防止再次计算
3. 再次获取的时候，直接返回缓存的值，当被依赖发生改变后，会触发依赖的 update 并将 Watcher.dirty 置为 true，data 导致页面重新渲染，触发 computer 的 getter，执行 watcher.evalute 重新计算值。

## watch

initWatch的过程中其实就是实例化new Watcher完成观察者的依赖收集的过程，在内部的实现当中是调用了原型上的Vue.prototype.$watch方法。
而自己定义的 watch
实际上就是 watcher 修改值后的一个回调