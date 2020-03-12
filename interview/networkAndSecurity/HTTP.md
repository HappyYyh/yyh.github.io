---
title: HTTP
permalink: /interview/networkAndSecurity/HTTP
key: networkAndSecurity-HTTP
---

### 【HTTP在OSI里哪一层】

**HTTP简介：**

> HTTP（超文本传输协议，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。

HTTP处在应用层可以点击[《OSI七层模型》](/interview/networkAndSecurity/start.html)查看对比。



### 【HTTP1.0/1.1/2.0】

- **HTTP1.0**

  HTTP 协议老的标准是HTTP/1.0，为了提高系统的效率，HTTP 1.0规定浏览器与服务器只保持短暂的连接，浏览器的**每次请求都需要与服务器建立一个TCP连接**，服务器完成请求处理后立即断开TCP连接，服务器不跟踪每个客户也不记录过去的请求。

- **HTTP1.1**

  HTTP 1.1支持**持久连接**（HTTP/1.1的默认模式使用带流水线的持久连接），在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟。

  HTTP/1.1相较于 HTTP/1.0 协议的区别主要体现在：

  1.  缓存处理

  2.  带宽优化及网络连接的使用

  3.  错误通知的管理

  4.  消息在网络中的发送

  5.  互联网地址的维护

  6.  安全性及完整性

- **HTTP2.0**

  HTTP2.0新特性：

  - **多路复用 (Multiplexing)**

    多路复用允许同时通过单一的 HTTP2.0 连接发起多重的请求-响应消息。

  - **二进制分帧**

     HTTP2.0 在应用层( HTTP2.0 )和传输层(TCP or UDP)之间增加一个二进制分帧层。在不改动 HTTP/1.x 的语义、方法、状态码、URI 以及首部字段的情况下, 解决了HTTP1.1 的性能限制，改进传输性能，实现低延迟和高吞吐量。

  - **首部压缩（Header Compression）**

    HTTP/1.1并不支持 HTTP 首部压缩，为此 SPDY 和 HTTP/2 应运而生， SPDY 使用的是通用的DEFLATE 算法，而 HTTP/2 则使用了专门为首部压缩而设计的 HPACK 算法。

  - **服务端推送（Server Push）**

    服务端推送是一种在客户端请求之前发送数据的机制。在 HTTP/2 中，服务器可以对客户端的一个请求发送多个响应。




### 【WebSocket和HTTP2.0的区别】

WebSocket是一个双向通信协议，基于HTTP1.1（暂时不支持 HTTP/2），本质上是基于TCP；

HTTP/2基于SPDY；

**区别：**

1. 加密与否：
   - WebSocket 支持明文通信 `ws://` 和加密 `wss://`，
   - 而 HTTP/2 协议虽然没有规定必须加密，但是主流浏览器都只支持 HTTP/2 over TLS.
2. 消息推送：
   - WebSocket是全双工通道，可以双向通信。而且消息是直接推送给 Web App.
   - HTTP/2 虽然也支持 Server Push，但是服务器只能主动将资源推送到客户端缓存！并不允许将数据推送到客户端里跑的 Web App 本身。服务器推送只能由浏览器处理，不会在应用程序代码中弹出服务器数据，这意味着应用程序没有 API 来获取这些事件的通知。



### 【HTTP报文结构，HTTP首部字段】   

HTTP通信过程包括客户端往服务器端发送请求以及服务器端给客户端返回响应两个过程。

在这两个过程中就会产生请求报文和响应报文。

**报文结构**：报文首部 + (可选)报文主体(两者通过空行`CR + LF`来划分)

**首部字段：**

HTTP 首部字段是由首部字段名和字段值构成的，中间用冒号“：”分隔。

另外，字段值对应单个 HTTP 首部字段可以有多个值。

当 HTTP 报文首部中出现了两个或以上具有相同首部字段名的首部字段时，这种情况在规范内尚未明确，根据浏览器内部处理逻辑的不同，优先处理的顺序可能不同，结果可能并不一致。

**首部字段名 冒号 字段值**

Content-Type ： text/html

Keep-Alive ： timeout=30, max=120

HTTP报文结构基本格式举例：

~~~http
GET /system/swagger-resources/configuration/ui HTTP/1.1
Host: localhost:9080
Connection: keep-alive
Accept: application/json
Sec-Fetch-Dest: empty
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36
Content-Type: application/json
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Referer: http://localhost:9080/system/swagger-ui.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cookie: JSESSIONID=0CE26DB11E614E30B80EB8B5B6C967EC;
~~~



### 【HTTP的Keep-Alive】

**由来：**

通常一个网页可能会有很多组成部分，除了文本内容，还会有诸如：js、css、图片等静态资源，有时还会异步发起AJAX请求。只有所有的资源都加载完毕后，我们看到网页完整的内容。然而，一个网页中，可能引入了几十个js、css文件，上百张图片，如果每请求一个资源，就创建一个连接，然后关闭，代价实在太大了。

基于此背景，我们希望连接能够在**短时间内**得到复用，在加载同一个网页中的内容时，尽量的复用连接，这就是HTTP协议中keep-alive属性的作用。

**概念：**

普通的http连接是客户端连接上服务端，然后结束请求后，由客户端或者服务端进行http连接的关闭。下次再发送请求的时候，客户端再发起一个连接，传送数据，关闭连接。这么个流程反复。

但是一旦客户端发送`connection:keep-alive`头给服务端，且服务端也接受这个`keep-alive`的话，两边对上暗号，这个连接就可以**复用**了，一个http处理完之后，另外一个http数据直接从这个连接走了。减少新建和断开TCP连接的消耗。

**使用：**

- HTTP的Keep-Alive是**HTTP1.1**中**默认开启**的功能。通过headers设置"Connection: close "关闭
- 在HTTP1.0中是**默认关闭**的。通过headers设置"Connection: Keep-Alive"开启。



### 【HTTP的长连接和实现原理】

**短连接和长连接概念：**

在HTTP/1.0中，默认使用的是**短连接**。也就是说，浏览器和服务器每进行一次HTTP操作，就建立一次连接，但任务结束就中断连接。如果客户端浏览器访问的某个HTML或其他类型的 Web页中包含有其他的Web资源，如JavaScript文件、图像文件、CSS文件等；当浏览器每遇到这样一个Web资源，就会建立一个HTTP会话。

但从 HTTP/1.1起，默认使用**长连接**，用以保持连接特性。使用长连接的HTTP协议，会在响应头有加入这行代码：

`Connection:keep-alive`在使用长连接的情况下，当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的 TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接。Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。实现长连接要客户端和服务端都支持长连接。

**实现原理：**

HTTP协议的长连接，实质上是TCP协议的长连接。

TCP长连接的操作步骤是：建立连接——数据传输...（保持连接）...数据传输——关闭连接



### 【Post和Get区别】 

**区别：**

1. **GET提交的数据放在URL中，POST则不会。**

   这是最显而易见的差别。这点意味着GET更不安全（POST也不安全，因为HTTP是明文传输抓包就能获取数据内容，要想安全还得加密）

2. **GET回退浏览器无害，POST会再次提交请求**（GET方法回退后浏览器再缓存中拿结果，POST每次都会创建新资源）

3. **GET提交的数据大小有限制1024个字符**（是因为浏览器对URL的长度有限制，GET本身没有限制），POST没有

4. **GET可以被保存为书签，POST不可以。**

5. **GET能被缓存，POST不能。**

6. **GET只允许ASCII字符，POST没有限制。**

7. **GET会保存再浏览器历史记录中，POST不会。**

总之，两者之间没有本质区别，区别就在于数据存储的位置。各自有适用环境，根据需求选择合适的方法即可。



### 【HTTP协议Cokkie相关】 

**概念：**



### 【如何让无状态的HTTP协议使得登录过程变得有状态】  

**无状态解释：**

HTTP 是一种不保存状态，即无状态（stateless）协议。

HTTP协议自身不对请求和响应之间的通信状态进行保存。也就是说在 HTTP 这个级别，协议对于发送过的请求或响应都不做持久化处理。

使用 HTTP 协议，每当有新的请求发送时，就会有对应的新响应产生。协议本身并不保留之前一切的请求或响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把 HTTP协议设计成如此简单的。

**解决办法:**

HTTP/1.1 虽然是无状态协议，但为了实现期望的保持状态功能，于是引入了 Cookie 技术。

有了Cookie 再用 HTTP 协议通信，就可以管理状态了。(服务端Session也可以)
