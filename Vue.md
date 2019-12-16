### Runtime only 与 Runtime + Compiler

runtime only(只包含运行时的版本)，runtime + compiler(同时包含编译器和运行时的版本)，区别仅在于后者包含了一个编译器。

通俗来讲，编译器是一个提供了将**源代码**转化为**目标代码**的工具，也就是将 **template** 模板转换编译为可执行 **js** 的功能。

#### Runtime + Compiler

使用 **template** 进行编写，编译器会自动将模板字符串编译成渲染函数的代码，也就是 render 函数。

```javascript
// 需要编译器
new Vue({
  template: '<div> {{ hi }}</div>'
})
```

template -> ast -> render -> vDom -> 真实Dom -> 页面

也就是 template 最终也是被渲染为 render 函数

#### Runtime Only

只包含运行时的代码，拥有创建 Vue 实例、渲染并处理 vDom 等功能，也就是除了编译器外的完整代码。

```javascript
// 不需要编译器
new Vue({
  render(h) {
    return h('div', this.hi)
  }
})
```

有两种使用方式：

1. 在选项中直接手写 render 函数定义渲染过程
2. 借助 **vue-loader** 插件。在使用 webpack 开发时，虽然使用的 template 标签进行的开发，但是插件已经帮我们编译好了。

#### 区别

runtime-only： 将template在打包的时候，就已经编译为 render函数

runtime-compiler： 在运行的时候，才去编译 template

### 挂载流程

![vue 编译流程图](static/vue&#32;render&#32;流程.png)

确定挂载节点，编译模板为 render 函数，渲染函数转换为 vDom，创建真实节点。