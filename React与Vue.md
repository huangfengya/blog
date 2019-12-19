## 区别

### 监听数据变化的实现原理不同

- Vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就可以达到很好的性能

- React 默认是通过比较引用的方式进行的，如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的 vDom 重新渲染

为什么 React 不精确监听数据变化呢？这是因为设计理念的不同：Vue 使用的是可变的数据，而 React 更强调数据的不可变。但是没有好坏之分。

### HOC 和 mixins

在 Vue 中组合不同功能的方式是通过 mixins，React 中使用的是 HOC（高阶组件）

React 为啥使用 HOC 而不是 mixins

1. es6 class 不支持 mixins
2. mixins 会修改 state，开发者无法直接确定 state 来自哪里，如果有多个 mixins 更甚
3. 如果使用多个 mixins，设定 state 可能会引起 state 冲突
4. HOC 可以接受一个组件作为参数，并且将组件作为其返回值；或者用装饰器实现

那为啥 Vue 使用 mixins 呢？Vue 做了一些处理：

1. 如果混入的是对象的话，会递归合并为一个对象，并以当前组件的优先
2. 如果混入的是钩子函数的话，先调用混入的钩子，在调用当前组件的钩子。

### 模板渲染方式不同

表面上：

- React 是通过 JSX 渲染模板
- 而 Vue 是一种拓展的 HTML 语法进行渲染

深层上：

- React 并不依赖 JSX，React 是在组件 js 代码中，通过原生 js 实现模板中的常见语法
- Vue 是在和组件 js 代码分离的单独模板中，通过指令实现的，如 v-if v-show
  
### Vuex 和 Redux 的区别

1. Redux 使用的是不可变数据，而 Vue 的数据可变的。Redux 每次都是用新的 state 替换旧的 state；而 Vuex 是直接修改 state 里面的值
2. Redux 在监测数据变化的时候，是通过 diff 比较差异的，这也是为啥不推荐直接改 state 里面的值；Vuex 和 Vue 的原理是一样的，也是通过 getter/setter 来比较的（内部会直接创建一个 Vue 实例来跟踪数据变化） 
