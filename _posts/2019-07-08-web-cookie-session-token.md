---
layout: article
title: 弄懂 Cookie、Session 和 Token
tags: Web

lang: zh-Hans
key: Web_Cookie_Session_Token
pageview: true
toc: true
show_subscribe: false
---

## 一、Cookie

Http 是一个无状态协议，也就是说这一次请求和上一次请求时没有任何关系的，无状态的优点的速度快，但是不能关联页面与页面之间的关系，比如登录一个商城，不可能要求用户每进入一个新的界面都重新登录一遍，所有需要一种技术手段来保持浏览状态，Cookie 就是这样的一种技术。

Cookie 指的是浏览器里面能永久存储的一种数据，仅仅是浏览器实现的一种数据存储功能。

Cookie 由服务器生成然后发送给浏览器的，浏览器把 Cookie 以键值对的形式保存到某个目录下的文本文件内，下一次请求同一网站时会把该 Cookie 发送给服务器。由于 Cookie 是存在客户端上的，所以浏览器加入了一些限制确保 Cookie 不会被恶意使用，同时不会占据太多磁盘空间，所以每个域的 Cookie 数量是有限的。

客户端访问服务器的流程如下：

- 客户端发送一个 http 请求到服务器端。

- 服务器端接受客户端请求后，发送一个 http 响应到客户端，这个响应头，其中就包含 Set-Cookie 头部。

- 客户端发起的第二次请求，假如服务器给了 set-Cookie，浏览器会自动在请求头中添加 cookie。

- 服务器接收请求，分解 cookie，验证信息，核对成功后返回 response 给客户端。

<div align="center">  <img src="img/web_cookie_session_token_1.png" width="60%"/> </div><br>


## 二、Session

Session 是针对服务端来说的，是一种抽象概念，用来保存认证用户信息。

服务器使用 Session 把用户的信息临时保存在了服务器上，用户离开网站后 Session 会被销毁。这种用户信息存储方式相对 Cookie 来说更安全，可是 Session 有一个缺陷：如果 Web 服务器做了负载均衡，那么下一个操作请求到了另一台服务器的时候 Session 会丢失。

Cookie 只是实现 Session 的一种方案，现在大多都是使用 Session + Cookie 的方式，Session 只需要在客户端保存一个 id，大量的数据保存到服务端。

如果只用 Cookie 不用 Session，那么账户信息全部保存在客户端，一旦被劫持，全部信息都会泄露。并且客户端数据量变大，网络传输的数据量也会变大。

## 三、Token

Token 是用户身份的验证方式，也称为令牌。

### 1. 组成

- uid: 用户唯一身份标识
- time: 当前时间的时间戳
- sign: 签名, 使用 hash/encrypt 压缩成定长的十六进制字符串，以防止第三方恶意拼接
- 固定参数(可选): 将一些常用的固定参数加入到 token 中是为了避免重复查库

### 2. 存放

Token 在客户端一般存放于 localStorage，cookie，或 sessionStorage 中。在服务器一般存于数据库中。

### 3. 认证流程

- 用户登录，成功后服务器返回Token给客户端。
- 客户端收到数据后保存在客户端。
- 客户端再次访问服务器，将token放入headers中。
- 服务器端采用filter过滤器校验。校验成功则返回请求数据，校验失败则返回错误码。

## 四、CSRF

CSRF（Cross Site Request Forgery, 跨站域请求伪造）是一种网络的攻击方式。

CSRF 攻击可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击站点，从而在并未授权的情况下执行在权限保护之下的操作。

比如说，受害者 Bob 在银行有一笔存款，通过对银行的网站发送请求 `http://bank.example/withdraw?account=bob&amount=1000000&for=bob2` 可以使 Bob 把 1000000 的存款转到 bob2 的账号下。通常情况下，该请求发送到网站后，服务器会先验证该请求是否来自一个合法的 session，并且该 session 的用户 Bob 已经成功登陆。黑客 Mallory 自己在该银行也有账户，他知道上文中的 URL 可以把钱进行转帐操作。Mallory 可以自己发送一个请求给银行：`http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory`。但是这个请求来自 Mallory 而非 Bob，他不能通过安全认证，因此该请求不会起作用。这时，Mallory 想到使用 CSRF 的攻击方式，他先自己做一个网站，在网站中放入如下代码： `src=”http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory”`，并且通过广告等诱使 Bob 来访问他的网站。当 Bob 访问该网站时，上述 url 就会从 Bob 的浏览器发向银行，而这个请求会附带 Bob 浏览器中的 cookie 一起发向银行服务器。大多数情况下，该请求会失败，因为他要求 Bob 的认证信息。但是，如果 Bob 当时恰巧刚访问他的银行后不久，他的浏览器与银行网站之间的 session 尚未过期，浏览器的 cookie 之中含有 Bob 的认证信息。这时，悲剧发生了，这个 url 请求就会得到响应，钱将从 Bob 的账号转移到 Mallory 的账号，而 Bob 当时毫不知情。等以后 Bob 发现账户钱少了，即使他去银行查询日志，他也只能发现确实有一个来自于他本人的合法请求转移了资金，没有任何被攻击的痕迹。而 Mallory 则可以拿到钱后逍遥法外。

CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。


## 参考

[彻底理解cookie，session，token](https://www.cnblogs.com/moyand/p/9047978.html)

[彻底弄懂session，cookie，token](https://segmentfault.com/a/1190000017831088)

[Cookie、Session、Token那点事儿](https://www.jianshu.com/p/bd1be47a16c1)

[浅谈CSRF攻击方式](https://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html)

[CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/index.html)







