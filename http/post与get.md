### 通常来说

1. GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息
2. GET参数通过URL传递，POST放在Request body中。
3. GET请求会被浏览器主动cache，而POST不会，除非手动设置。
4. GET请求在URL中传送的参数是有长度限制的，而POST么有。


### 实际上

基本木有区别。

GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。 

如果你用GET服务，在request body偷偷藏了数据，不同服务器的处理方式也是不同的，有些服务器会读出数据，有些服务器直接忽略，所以，虽然GET可以带request body，也不能保证一定能被接收到

### 轻微区别

GET产生一个TCP数据包；POST产生两个TCP数据包。

对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；

而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）

当然，并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。