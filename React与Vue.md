## 区别

### 监听数据变化的实现原理不同

- Vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就可以达到很好的性能

- React 默认是通过比较引用的方式进行的，如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的 vDom 重新渲染

为什么 React 不精确监听数据变化呢？这是因为设计理念的不同：Vue 使用的是可变的数据，而 React 更强调数据的不可变。但是没有好坏之分。