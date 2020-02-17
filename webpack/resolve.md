object 类型。

webpack 在启动后会从配置的入口模块出发找出所有依赖的模块，resolve 配置 webpack 如何寻找模块所对应的文件。

## alias

配置别名，可以把原导入路径映射成一个新路径

```webpack
resolve: {
    alias: {
        componments: './src/components/'
    }
}
```

这样配置后 **import button from 'componments/button** 等价于 **import button from './src/components/button'**;

同时 alias 也支持 **$** 来缩小范围到只命中以关键字结尾的导入语句

```webpack
resolve: {
    alias: {
        'vue$': 'vue/dist/vue.esm.js'
    }
}
```

**import 'vue'** 就等价于 **import 'vue/dist/vue.esm.js'**

## mainFields

有一些第三方模块会针对不同的环境提供几份代码，mainFields 用来决定首先使用哪个模块。

```webpack
// 默认值
resolve: {
    mainFields: ["browser", "module", "main"]
}

// 一般模块的代码位于该包的 package.json 下
{
    main: 'build/d3.node.js',
    browser: 'build/d3.js',
    modules: 'index'
}
```

## extensions

在导入语句没带文件后缀时，webpack 会自动带上后缀尝试文件是否存在。

