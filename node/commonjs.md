## CommonJs 的模块规范

CommonJs 是一种使用广泛的 JavaScript 模块化规范，核心思想是通过 require 方法来同步的加载依赖的其他模块，通过 module.exports 导出需要暴露的接口

### 模块引用

在 CommonJs 规范中，使用 **require** 方法，这个方法接受 **模块标识**，以此引入一个模块的 API 到当前上下文中。

```javascript
const path = require('path')
```

### 模块定义

上下文提供了 **exports** 对象用于导出当前模块的方法或变量，并且他是唯一的导出的出口。

在模块中，还存在一个 **module** 对象，他代表模块自身，而 exports 是 module 的属性。

在 Node 中，一个文件就是一个模块，将方法挂载在 exports 对象作为属性即可定义导出的方式。

```javascript
exports.sum = function(a, b) {
  return (a + b)
}
```

### 模块标识

模块标识起始就是传递给 require 方法的参数，它必须是符合小驼峰命名的字符串，或者相对路径/绝对路径

## Node 的模块实现

### 在 Node 中引入模块的步骤

1. 路径分析
2. 文件定位
3. 编译执行

### 模块分类

#### 原生模块

**http**、**fs**、**path**、**events** 等模块都是 Node 提供的模块，这些模块在 Node 源代码的编译过程中被编译成二进制。在 Node 启动的时，部分原生代码就被直接加入内存中，所以原生模块引入时文件定位和编译执行这两个步骤可以省略掉，并且在路径分析中优先判断，所以加载速度最快。

#### 文件模块

存储于用户的某个位置，在使用时动态加载，需要完成路径分析、文件定位、编译执行过程，速度比原生模块慢。

根据文件模块的后缀有三种加载方式：

- .js 需要先读入内存在运行
- .json fs 读入内存，转化为 json 对象
- .node 为编译后的二进制 C/C++ 模块，可以直接使用

#### 第三方模块

- 如果 require 函数只指定名称则视为从 **node_modules** 下面加载文件
- 第三方模块的查询路径包括 **module.paths** 和全局目录
- 加载最慢

### 加载策略

#### 优先从缓存加载

Node 对引入的模块都会进行缓存，以减少二次引入时的开销，与前端浏览器缓存静态脚本不同，Node 缓存的是编译和执行后的对象。

不论是原生模块还是文件模块，require 对相同模块的加载都是一律采用 **缓存优先** 的策略。

![文件加载顺序](../static/cache&#32;order.png)

#### 路径分析和文件定位

1. 路径分析

**module.paths** 可以打印所有的可加载的路径

路径顺序是从当前的目录一直到根目录，在每一层级的 node_modules 文件夹

所以当前文件的路径越深，模块查找耗时越多，这也是第三方模块加载速度最慢的原因

2. 文件定位

- 文件扩展名 扩展名顺序 **.js > .node > .json**

尝试过程中需要调用 fs 模块同步阻塞判断文件是否存在，因为是单线程，会引起性能问题，推荐在 .json 和 .node 文件时带上拓展名

- 目录分析和包 require 分析文件拓展名之后，可能没有查到对应文件，但却得到一个目录，此时 Node 会将该目录当做一个包来处理

首先，Node 会在当前目录下查找 package.json，从中取出 **main** 属性指定的文件进行定位，如果文件缺少扩展名则会进入扩展名分析的步骤。如果 main 指定的文件名错误，或者没有 package.json 文件，Node 将 **index** 作为默认文件名，然后依次查找 index.js index.node index.json