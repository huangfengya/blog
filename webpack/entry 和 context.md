entry 对象是用于 webpack 查找启动并构建 bundle。其上下文是入口文件所处的目录的绝对路径的字符串。

## context

选填，基础目录，**绝对路径**，用于从配置中解析 entry 和 loader，默认为当前目录

## entry

起点或者是应用程序的入口。

不能使用动态加载的模块作为入口。

### entry 类型

入口模块，类型可以为 String/Object/Array

如果传递的为 Array，那么数组的每一项都会执行。

注意：当结合 output.library 选项时：如果传入数组，则只导出最后一项。

### chunk

chunk 表示一个文件，默认情况下 webpack 的输入是一个入口文件，输出也是一个文件，这个文件就是一个 chunk。

- 如果入口为 String/Array ，chunk 会被命名为 **main**
- 如果为 Object，则 key 为 chunk 的名称

### 动态入口

entry 的可能会受到某些因素的影响而不能写成静态的值。

```
entry: () => {
    return {
        a: "./pages/a",
        b: ["./pages/b1", "./pages/b2"]
    }
}
```

