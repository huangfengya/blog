## SSO 与 CAS

### SSO

SSO 是英文 Single Sign On 的缩写，也就是单点登录。顾名思义，也就是把两个及以上产品中的用户登录逻辑抽离出来，达到只输入一次用户名密码，就能同时登录多个产品的效果。

优点：

1. 提升用户体验
2. 避免重复开发

### CAS

SSO 是一种架构，而 CAS 则是实现 SSO 的一种手段。CAS（Central Authentication Service）中心授权服务，是个开源协议。

## SSO 的演进与分类

### 同域 SSO

同域 SSO 是最简单的一种情况，就是所有的产品在同一个域名下：

1. 用户访问产品 a，向后台服务器发送登录请求
2. 登录认证成功，服务器吧用户的登录信息写入 session
3. 服务器为该用户生成一个 cookie，并且加入到 response header 中，随着请求返回并写入到浏览器
4. 下一次，当用户访问产品 b 时，因为都在同一域名下，所以请求时会自动带上之前的 cookie，后台服务器也就可以通过该 cookie 来验证登录状态了

### 同父域 SSO

同父域 SSO 是同域 SSO 的简单升级，唯一的区别在于服务器设置 cookie 的时候，要把 cookie 的 domain 设置为父域。

### CAS

#### 票据

- TGT: Ticket Grangting Ticket

TGT 是 CAS 为用户签发的登录票据，拥有了 TGT，用户就可以证明自己在 CAS 成功登录过。TGT 封装了 Cookie 值以及此 Cookie 以及此 Cookie 值对应的用户信息

- TGC: Ticket grangting Cookie

CAS Server 生成 TGT 放入自己的 Session 中，而 TGC 就是这个 Session 的唯一标识（SessionId）。（key: value 结构，TGC: TGT）TGC 以 cookie 的形式保存在浏览器中，每个请求都会尝试携带 TGC。

- ST: Service Ticket

ST 是 CAS 为用户签发的访问某一 Service 的票据。用户访问 Service 时，Service 发现用户没有 ST，则要求用户去 CAS 获取 ST。用户向 CAS 发出获取 ST 的请求，CAS 发现用户有 TGT，则签发一个 ST，返回给用户，用户拿着 ST 去访问 Service，Service 拿 ST 去 CAS 验证，验证通过后允许访问资源

#### 具体步骤

1. 用户访问产品 a，域名为 a.com
2. 由于用户没有 a Cookie，所以 a 服务器返回 http 重定向，重定向的地址是 SSO 服务器的地址，同时 URL 的 query 中通过参数指明登录后回跳的页面。url 形式如 sso.abc.com/login?service=http://a.com
3. 由于用户没有携带在 SSO 服务器登录的 TGC，所以 SSO 判断用户未登录，给用户显示统一登录界面，用户在 SSO 的页面上进行登录
4. 登录成功后，SSO 服务器构建用户在 SSO 登录的 TGT，同时返回一个 http 的重定向：
   - 重定向地址为之前写在 query 里的 a 页面
   - 重定向地址 query 中包含 sso 服务器派发的 ST
   - 重定向的 http reponse 中包含写 Cookie 的 header，这个 Cookie 代表用户在 SSO 的登录状态，也就是 TGC
5. 根据 ST，a 产品向 SSO 服务器发送请求，SSO 服务器验证票据的有效性，验证成功后，a 产品知道用户已经在 SSO 登录了，构建用户登录 Session 记为 a Session，并将 Cookie 写入浏览器。注意：此处的 Cookie 和 Session 保存的是用户在 a 产品登录状态，和 CAS 无关
6. 之后用户访问 b.com
7. 由于用户没有 b Cookie，所以 b 服务器返回 http 重定向，重定向的地址是 SSO 服务器的地址，去询问用户在 SSO 中的状态
8. 浏览器重定向到 SSO，因为之前已经写入了携带 TGC 的Cookie，所以可以从 SSO 服务器拿到，根据 TGC 去查找 TGT，如果找到，则说明用户已经登录过
9. SSO 返回一个重定向，并携带 ST，注意每次的 ST 都是不一样的
10. 剩下的步骤就和 a 登录一样了

#### 问题

由于 ST 可能会被截取，所以需要加一些额外的验证因子，如 ip，sessionId 等
