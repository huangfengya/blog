用于决定如何处理项目中的不同类型的模块

## noParse

不需要解析的文件，如 jQuery, lodash，(需要自行判断)，可以提高构建性能。

```
noParse: /jquery|lodash/
```

## rules

array 类型，配置模块的读取和解析规则，通常用来配置 loader。配置一项 rules 时大致通过以下方式：

1. 条件匹配：通过 test(匹配文件名), include(指定文件夹), exclude(不需要解析的文件夹) 来命中要应用规则的文件;
2. 应用规则：通过 use(array类型) 配置来应用 loader, 同时也可以给 loader 传入参数( ? 或 options)；
3. 执行顺序：默认为**从右往左**，可以通过 Rule.enforce 来修改执行顺序

```webpack
module: {
    noParse: /jquery|lodash/,
    rules: [
        {
            test: /\.js$/  // 命中 js 文件
            use: [
                "babel-loader?cacheDirectory"   // 通过 ? 传入 loader 配置
            ],
            include: 'src/' // 只命中 src/ 文件夹下的文件
            exclude: 'node_modules'  // 不匹配 node_modules 下的文件
        },
        {
            test: /jpg|jpeg|png|svg$/,
            use: [
                {
                    loader: 'file-loader',      // 使用 options 传入 loader 配置
                    options: {
                        filename: '[name].[ext]?[hash:8]'
                    }
                }
            ]
        }
    ]
}
```

### Rule.enforce

值为 post(最后) | pre(最前)，用于决定 loader 的执行顺序

### Rule.parser

解析选项对象：

- 设置为 **false**，将禁用解析器
- 设置为 **true**，或不修改将其保留为 **undefined**，可以启用解析器。

不过，有些解析器也可以接受 Object。

```webpack
module: {
    rules: [
        {
            test: /\.js$/,
            use: ["babel-loader"],
            parse: {
                amd: false,  // 禁用 amd
                commonjs: false // 禁用 commonjs
                ···
                ···
            }
        }
    ]
}
```