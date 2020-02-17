插件，用于扩展 webpack 的功能。

## 常用插件

1. html-webpack-plugin

- 为 HTML 文件引入外部资源；
- 可以生成 HTML 入口文件，简单来说就是可以生成多页面应用。

2. clean-webpack-plugin

用来删除文件/文件夹，可以清理以前的 dist 文件夹

3. CommonsChunkPlugin

webpack 自带，用于提取公共代码。
在 webpack4 中被删除，改为 SplitChunksPlugin

4. uglifyjs-webpack-plugin

用于压缩代码

5. HotModuleReplacementPlugin

webpack 自带，热替换插件