output 包括了一组选项，用于指示 webpack 如何输出以及输出文件的位置

## filename

输出文件的名称，string 类型。

如果只有一个输出文件，可以直接写成静态的：

```
filename: 'bundle.js'
```

如果有多个文件的话，可以通过 chunk 来自动命名

```
filename: "[name].js"
```


可用变量名 | 含义
---|---
id | chunk 的唯一标识，从 0 开始
name | chunk 的名称
hash | chunk 的唯一标识的 hash 值
chunkhash | chunk 内容的 hash 值

其中 hash 和 chunkhash 的长度是可以指定的，[hash:8] 表示取**8位**的 hash 值，默认**20位**

> 注意，ExtractTextWebpackPlugin 插件是使用 **contenthash** 而不是 **chunkhash**，原因是该插件提取出来的内容是代码本身内容而非由一组模块组成的 chunk
> webpack 4.0 以后使用 mini-css-extract-plugin 插件，上一个插件无效, 另外该插件与 style-loader 不兼容。

## path

配置输出文件的路径，必须为 string 类型的绝对路径。

```
path: path.resolve(__dirname, 'dist')
```

## publicPath

实际上就是给页面加一个公共前缀。可以为 http 地址，也可以为相对地址。

> 注意：css 中的也会被加上该前缀，一不小心 404 就会等着你。

```
// 打包配置
output: {
    filename: 'bundles.js',
    publicPath: 'https://cdn.baidu.com/'
}

// 插入html后的显示
<script src="https://cdn.baidu.com/bundles.js"></script>
```

## sourceMapFilename

source-map 的文件名，只有在 **devtool** 开启时有效，默认为 "文件名.扩展名.map"

