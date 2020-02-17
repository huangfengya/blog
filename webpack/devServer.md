本地开发服务器

object 类型

## hot

配置是否启用模块热替换功能。默认行为是通过刷新页面来展示新内容。

## inline

用于配置是否自动注入 chunk 里面去，默认自动注入。

- 开启：使用 inline 模式，devServer 会在构建完变化的代码后控制网页刷新（所有打开该页面的浏览器）。
- 关闭：iframe 模式，devServer 无法实时刷新该页面，但是在 **http://localhost/webpack-dev-server/** 路径下可以实时刷新，而且页面是以 iframe 形式展示的，通过刷新 iframe 实时展示。

一般开启 inline。

## historyApiFallback

用于开发单页面应用。这样在服务器命中任何路由时都只返回一个HTML文件。

```
devServer: {
    // 简单配置，只返回 index.html
    historyApiFallback: true
    
    // 多个单页面
    historyApiFallback: {
        rewrites: [
            // /user 开头的都返回 user.html
            {from: /^\/user/, to: '/user.html'},
            {from: /^\/game/, to: '/game.html'},
            
            // 其他的都返回 index.html
            {from: '/./', to: '/index.html'}
        ]
    }
}
```

## contentBase

配置 devServer 的文件根目录。默认情况为当前执行目录，一般不需要配置。

## headers 

在 http 响应中注入一些 http 响应头

## host / port

host 配置 devServer 服务监听的地址。如果局域网中其他设备要访问的话，可以设置为 0.0.0.0

port 配置 devServer 服务监听的端口，默认 8080

## https

是否启用 https

```
devServer: {
    // 简单配置
    https: true
    
    // 如果要使用自己的证书
    https: {
        key: fs.readFileSync('path/server.key'),
        cert: fs.readFileSync('path/server.crt'),
        ca: fs.readFileSync('path/ca.pem')
    }
}
```

## compress

是否启用压缩

## open

发否自动打开页面

## proxy

用于转发请求，解决开发时跨域

```
proxy: {
    "/api": "http://localhost:8080"
}
```
如果初始请求地址为 /api/user，代理后的则为 http://localhost:8080/api/user

如果不需要一直传递 /api，则可以重新路径

```
proxy: {
    "/api": {
        target: "http://localhost:8080",
        pathRewrite: {"^/api": ""}
    }
}
```

默认情况下，不能运行在使用了无效证书的 https 服务器上，可以通过修改 secure 为 false 来运行。