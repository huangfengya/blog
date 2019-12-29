## 什么是 cookie

cookie 实际上是一小段文本信息，用于服务端和客户端之间的信息交流

## cookie 的属性

| 属性名 | 描述 |
| :- | :- |
| name | cookie 的名称，cookie 一旦创建则不可更改|
| value | cookie 的值，如果值为 Unicode 字符，则需要字符编码；如果为二进制数据，则需要 base64 编码 |
| maxAge/Expreies | cookie 的失效时间，单位为 秒。如果为正数，则在 maxAge 秒后失效，如果为负数，则表示为临时 cookie，关闭浏览器则失效；如果为 0，则表示删除该 cookie。默认为 -1 |
| secure | 该 cookie 是否仅被安全协议传输。安全协议包括有 https / ssl等。默认为 false |
| path | cookie 的使用路径。如果设置为 **/** 则本域名下 contextPath 都可以访问该 cookie；如果设置为 /sunlands/，则只要 contentPath 为 /sunlands 的程序可以访问该 cookie。注意结尾必须为 **/**
| domain | 可以访问该 cookie 的域名。如果设置为 .google.com，则所以以 google.com 结尾的域名都可以访问该 cookie。注意开头必须为 **.** |
| comment | 该 cookie 用处说明，浏览器显示 cookie 信息的时候显示该书名。 |
| version | cookie 使用的版本号 |
