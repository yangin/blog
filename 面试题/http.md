# 1. http1.0/1.1/2.0，https 的区别

- http1.0
  - 浏览器与服务器只保持短暂的连接，浏览器的每次请求都需要与服务器建立一个 TCP 连接
  - 80 端口
- http1.1
  - 引入了持久连接，即 TCP 连接默认不关闭，可以被多个请求复用(即默认开启 Keep-Alive)
  - 在同一个 TCP 连接里面，客户端可以同时发送多个请求
  - 虽然允许复用 TCP 连接，但是同一个 TCP 连接里面，所有的数据通信是按次序进行的，服务器只有处理完一个请求，才会接着处理下一个请求。如果前面的处理特别慢，后面就会有许多请求排队等着
  - 新增了一些请求方法, 如 `OPTIONS`、`PUT`、`DELETE`、`TRACE`、`CONNECT`
  - 新增了一些请求头和响应头，如 `Host`、`If-Modified-Since`、`If-Unmodified-Since`、`Accept-Language`、`Authorization`、`Cache-Control`、`Transfer-Encoding`、`Content-Length`、`Content-Type`、`Content-Encoding`、`Content-Range`、`Range`
  - 80 端口
- http2.0
  - 采用二进制格式而非文本格式
  - 完全多路复用，而非有序并阻塞的、只需一个连接即可实现并行
  - 使用报头压缩，降低开销
  - 服务器推送
    > 总结：
    >
    > - http2.0 相较于 http，主要是在传输效率上有所提升
    > - 在安全性上并没有提升，与 http 是一样的。
    > - http2.0 通常与 https 一起使用，来保证数据的安全性，基于 443 端口。
- https
  - https 即 HTTP + SSL/TLS，是以`安全`为目标的 HTTP 通道，简单讲是 `HTTP 的安全版`
  - 需要申请 SSL/TSL 证书，这是 https 安全的基础
  - 443 端口

# 2. get 与 post 的区别

- GET 在浏览器回退时是无害的，而 POST 会再次提交请求。
- GET 产生的 URL 地址可以被 Bookmark，而 POST 不可以。
- GET 请求会被浏览器主动 cache，而 POST 不会，除非手动设置。
- GET 请求只能进行 url 编码，而 POST 支持多种编码方式。
- GET 请求参数会被完整保留在浏览器历史记录里，而 POST 中的参数不会被保留。
- GET 请求在 URL 中传送的参数是有长度限制的，而 POST 没有。
- 对参数的数据类型，GET 只接受 ASCII 字符，而 POST 没有限制。
- GET 比 POST 更不安全，因为参数直接暴露在 URL 上，所以不能用来传递敏感信息。
- GET 参数通过 URL 传递，POST 放在 Request body 中

# 3.OSI 7 层模型、TCP/IP 的四、五层模型

### Q1: OSI 七层模型

- 应用层：提供为应用软件而设计的`接口`，以设置与另一应用软件之间的通信。例如：HTTP、HTTPS、FTP、Telnet、SSH、SMTP、POP3 等。
- 表示层：把数据`转换`为能与接收者的系统格式`兼容`并适合传输的`格式`。
  > - 转换：将 ASCII 码转换为 EBCDIC 码。
  > - 加密、解密：数据的加密与解密。
  > - 压缩：压缩可以减少需传送的数据量。
- 会话层：负责在数据传输中设置和`维护`计算机网络中两台计算机之间的`通信连接`。
- 传输层：对应用层的传递来的报文添加首部信息（如`端口号`等），然后在发送方与接收方分别进行`分段`与`重组`,进行`服务点寻址`。

  > - 分段：将应用层的报文分段成适合网络传输的`报文段`。
  > - 重组：将网络传输的报文段重组成应用层的报文。
  > - 服务点寻址：为了将消息传递给正确的进程（即端口号）
  > - 传输协议：TCP、UDP 等。
  >
  > 传输层由操作系统管理，是 OSI 模型的核心。

- 网络层：为传输层传递来的报文段添加首部信息（如发送端与接收端的 `IP 地址`），生成`网络层数据包`，并进行`逻辑层寻址`。
- 数据链路层：为网络层数据包添加首部信息（如 `MAC 地址`），封装成`帧`，并负责网络的`物理寻址`、错误侦测和改错。

  > - 流量控制：控制数据的传输速率，确保接收方来得及接收。
  > - 差错控制：发生差错时，重新传输数据。

- 物理层：负责在设备（如路由器）和物理传输介质（如线缆）之间的互通。规范化的将数字转换成电子、光子或者无线信号。

### 2. UDP 与 TCP

- TCP(Transmission Control Protocol，传输控制协议)

  - 面向连接，可靠
  - 数据按顺序到达
  - 数据最小错误
  - 重复数据被丢弃
  - 丢失、缺失的数据会重发
  - 流量拥塞控制

  > 应用场景：
  >
  > SMTP（电子邮件）、HTTP（网页）、FTP（文件传输）、Telnet（远程登录）等

- UDP(User Data Protocol，用户数据报协议)

  - 无链接，不可靠
  - 使用弱校验和算法检查错误
  - 用于对准时性要求高，可靠性要求低的流媒体（音频、视频、Voice over IP 等），或者建立可靠链接消耗太大的简单查询，如 DNS 解析。

  > 应用场景：
  >
  > DNS(域名转换)、SNMP（网络管理）、TFTP（文件传输）、NFS（远程文件服务器） 等

### 3. 5 层模型

- 应用层：应用层、表示层、会话层
- 传输层
- 网络层
- 数据链路层
- 物理层

### 4. 4 层模型

- 应用层：应用层、表示层、会话层
- 传输层
- 网络层
- 网络接口层：数据链路层、物理层

# 4. DNS

DNS(Domain Name System，域名系统)，它用于 TCP/IP 网络，将主机名或域名转换为实际 IP 地址。

### 域名缓存

- 浏览器 DNS 缓存：浏览器在获取网站域名的实际 IP 地址后会对其进行缓存(Chrome 默认缓存 60s),可通过 `chrome://net-internals/#dns` 查看
- 操作系统 DNS 缓存：操作系统的缓存其实是用户自己配置的 hosts 文件
- 路由器 DNS 缓存：路由器会缓存 DNS 记录，可通过路由器管理页面查看和重置
- ISP DNS 缓存：ISP（Internet Service Provider）互联网服务提供商(如移动，电信) 也会缓存 DNS 记录
  > 我们常说的 DNS 服务器地址，一般指的是 ISP 的 DNS 服务器地址，如移动安徽的 DNS 服务器地址为：211.138.180.2

### DNS 查询顺序

浏览器缓存 -> 操作系统缓存 -> 路由器缓存 -> ISP DNS 缓存 -> 根域名服务器（.com） -> 顶级域名服务器(baidu.com) -> 主域名服务器(m.baidu.com)

# 5. CDN

CDN (Content Delivery Network)，即内容分发网络，是构建在现有网络基础之上的智能虚拟网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户`就近获取所需内容`，降低网络拥塞，提高用户访问响应速度和命中率。

### CDN 执行过程

用户发起请求后，利用 DNS 解析，将域名解析为 CDN 节点的 IP 地址，然后通过 DNS 负载均衡，将用户请求分发到最优的 CDN 节点，最后 CDN 节点将用户请求的内容返回给用户。

- 负载均衡系统
  - 看用户的 IP 地址，查表得知地理位置，找相对最近的边缘节点
  - 看用户所在的运营商网络，找相同网络的边缘节点
  - 检查边缘节点的负载情况，找负载较轻的节点
  - 其他，比如节点的“健康状况”、服务能力、带宽、响应时间等
- 缓存代理
  - 缓存系统会有选择地缓存那些最常用的那些资源
  - 命中率：用户访问的资源恰好在缓存系统里，可以直接返回给用户，= 缓存命中的次数 / 总的请求数
  - 回源率：缓存里没有，必须用代理的方式回源站取，= 回源次数 / 总的请求数
    > 缓存系统也可以划分出层次，分成一级缓存节点和二级缓存节点。一级缓存配置高一些，直连源站，二级缓存配置低一些，直连用户
    >
    > 现在的商业 CDN 命中率都在 90% 以上

# 6. Http 常见状态码

- 1xx：指示信息，表示请求已接收，继续处理，常用于大数据上传。
  - 100：继续。客户端应继续其请求，如 post 上传数据时，服务器返回此状态码，表示服务器已收到请求的第一部分（options 部分），正在等待其余部分
  - 101：切换协议。服务器根据客户端的请求切换协议，主要用于 websocket 或 http2 升级
- 2xx：成功，表示请求已被成功接收、理解、接受
  - 200：请求成功
  - 204：请求成功，但没有资源返回
  - 206：请求成功，但只返回部分资源，一般用来做断点续传
- 3xx：重定向，表示需要客户端进一步操作才能完成请求
  - 301：永久重定向，表示请求的资源已被分配了新的 URL，用于新旧域名替换
  - 302：临时重定向，表示请求的资源已被分配了新的 URL，用于未登录的用户访问用户中心被重定向到登录页
  - 304：协商缓存，告诉客户端有缓存，直接使用缓存中的数据，返回页面的只有头部信息，是没有内容部分
- 4xx：客户端错误，表示客户端提交的请求有错误，如请求地址不存在、请求参数错误等
  - 400：请求参数错误
  - 401：未授权，表示客户端没有权限访问请求的资源
  - 403：禁止访问，表示服务器禁止访问请求的资源
  - 404：未找到，表示服务器上没有请求的资源
- 5xx：服务器错误，表示服务器在处理请求的过程中发生了错误
  - 500：服务器内部错误，表示服务器在执行请求时发生了错误
  - 503：服务器不可用，表示服务器暂时处于超负载或正在停机维护，无法处理请求
  - 504：网关超时

# 7. Http 常见的请求头

- Accept：指定客户端能够接收的内容类型，如：`Accept: text/plain, text/html`
- Accept-Charset：指定客户端能够接收的字符集，如：`Accept-Charset: utf-8`
- Accept-Encoding：指定客户端能够接收的内容编码，如：`Accept-Encoding: gzip, deflate`
- Authorization：用于超文本传输协议的认证的认证信息，如：`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`
- Cache-Control：指定请求和响应遵循的缓存机制，如：`Cache-Control: max-age=3600`
- Connection：表示是否需要持久连接（HTTP 1.1 默认进行持久连接），如：`Connection: keep-alive Connection: Upgrade`
- Cookie：HTTP 请求发送时，会把保存在该请求域名下的所有 Cookie 值一起发送给 Web 服务器，如：`Cookie: $Version=1; Skin=new;`
- Content-Type：表示具体请求中的媒体类型信息，如：`Content-Type: application/x-www-form-urlencoded`
- Date：表示请求发送的日期和时间，如：`Date: Tue, 15 Nov 2010 08:12:31 GMT`
- Expect：表示客户端需要服务器做出特定的行为，如：`Expect: 100-continue`
- Host：表示请求的域名，如：`Host: www.baidu.com`
- If-Match：表示只有请求内容与实体相匹配才有效，如：`If-Match: “737060cd8c284d8af7ad3082f209582d”`
  > 用作像 PUT 这样的方法中，仅当从用户上次更新某个资源以来，该资源未被修改的情况下，才更新该资源
- If-Modified-Since：表示如果请求的部分在指定时间之后被修改则请求成功，未被修改则返回 304 状态码, 用作协商缓存，如：`If-Modified-Since: Sat, 29 Oct 2010 19:43:31 GMT`
  > 用作像 GET 这样的方法中，允许在对应的 ETag 没有改变的情况下返回 304 状态码，响应体为空，节省了传输数据量
- If-Range：表示如果实体未改变，服务器发送客户端丢失的部分，否则发送整个实体，如：`If-Range: “737060cd8c284d8af7ad3082f209582d”`
  > 用作像 GET 这样的方法中，允许在对应的 ETag 没有改变的情况下返回 206 状态码，响应体为实体中的部分内容，节省了传输数据量
- User-Agent：客户端信息，如：`User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:23.0) Gecko/20100101 Firefox/23.0`
- Origin：用于 CORS 中，表示请求来自哪个源，如：`Origin: http://www.baidu.com`

# 8. 在浏览器地址栏输入 url 后，发生了什么

- URL 解析
  - 协议：http、https、ftp 等
  - 域名：www.baidu.com
  - 端口：80、443 等
  - 路径：/index.html
  - 查询参数：?id=1
  - 锚点：#top
- DNS 解析
  - 浏览器缓存
  - 操作系统缓存
  - 路由器缓存
  - ISP DNS 缓存
  - 根域名服务器
  - 顶级域名服务器
  - 主域名服务器
- TCP 连接
  - 三次握手
    - 客户端发送 SYN 包到服务器，进入 SYN_SEND 状态
    - 服务器收到 SYN 包，回应一个 SYN+ACK 包，进入 SYN_RECV 状态
    - 客户端收到 SYN+ACK 包，回应一个 ACK 包，进入 ESTABLISHED 状态
  - 四次挥手
    - 客户端发送 FIN 包到服务器，进入 FIN_WAIT_1 状态
    - 服务器收到 FIN 包，回应一个 ACK 包，进入 CLOSE_WAIT 状态
    - 服务器发送 FIN 包到客户端，进入 LAST_ACK 状态
    - 客户端收到 FIN 包，回应一个 ACK 包，进入 TIME_WAIT 状态
    - 服务器收到 ACK 包，进入 CLOSED 状态
- 发送 HTTP 请求
- 响应 HTTP 请求
- 页面渲染
  - 解析 HTML，生成 DOM 树
  - 解析 CSS，生成 CSSOM 树
  - 将 DOM 树和 CSSOM 树结合，生成渲染树
  - 根据渲染树计算每个节点的位置
  - 将渲染树绘制到屏幕上

# 9. 协商缓存、强制缓存

[参考](https://juejin.cn/post/7127194919235485733#heading-0)

> 缓存的设置都是由后端来设置的，前端只能通过设置请求头来控制缓存的使用

- 强制缓存: 不会向服务器发送请求，直接从缓存中读取资源

  使用方法

  - Expires（已弃用）：HTTP/1.0 的产物，表示资源过期时间，由服务器返回，如：`Expires: Wed, 21 Oct 2015 07:28:00 GMT`
    > 缺点：服务器时间和客户端时间可能不一致，如果修改了客户端时间，可能会出现缓存失效的情况
  - Cache-Control（推荐）：HTTP/1.1 的产物，表示资源缓存的最大有效时间，由服务器返回，如：`Cache-Control: max-age=3600`

    > - max-age：表示资源缓存的最大有效时间，单位为秒
    > - s-maxage：覆盖 max-age，只在代理服务器中生效
    > - public：表示资源可以被客户端和代理服务器缓存
    > - private：表示资源只能被客户端缓存
    > - no-cache：表示强制使用协商缓存
    > - no-store：表示禁止使用缓存，每次请求都要向服务器发送请求

  应用场景：

  - 带 hash 值的 js、css 文件名，都可以使用强缓存。
  - 不会变化的静态资源，如：logo、icon、图片 等

- 协商缓存: 会向服务器发送请求，如果缓存有效，服务器会返回 304 状态码，浏览器直接从缓存中读取资源

  使用方法：

  - Last-Modified：表示资源最后一次修改的时间，由服务器返回，如：`Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT`
    > 缺点：如果文件被修改了，但内容没有改变，也会导致缓存失效
    >
    > 当对浏览器向下兼容时需要使用此属性
  - ETag：表示资源的唯一标识，由服务器返回，如：`ETag: "737060cd8c284d8af7ad3082f209582d"`
    > 优先级高于 Last-Modified
  - If-Modified-Since：表示资源的最后修改时间，由客户端发送，如：`If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT`
  - If-None-Match：表示资源的唯一标识，由客户端发送，如：`If-None-Match: "737060cd8c284d8af7ad3082f209582d"`
    > 优先级高于 If-Modified-Sinc、ETag、Last-Modified、Cache-Control、Expires

  应用场景：

  - 对于不带 hash 值的文件名，如 index.html 文件，都可以使用协商缓存

### Q1: 协商缓存与强制缓存的区别

- 强制缓存不会向服务器发送请求，直接从缓存中读取资源
- 协商缓存会向服务器发送请求，如果缓存有效，服务器会返回 304 状态码，浏览器直接从缓存中读取资源

### Q2: 如何更新强制缓存资源

- 更新资源文件名
- 重置 max-age

# 10. 对 websocket 的理解

WebSocket，是一种网络传输协议，位于 OSI 模型的`应用层`。可在单个 TCP 连接上进行`全双工通信`，能更好的节省服务器资源和带宽并达到实时通迅

客户端和服务器只需要完成一次握手，两者之间就可以创建持久性的连接，并进行双向数据传输

### Q1: 特点

- 全双工通信
- 二进制帧传输
- 协议名：ws、wss
- 握手

  - Connection：必须设置为 Upgrade，表示客户端希望连线升级
  - Upgrade：必须设置为 websocket，表示客户端希望升级到 websocket 协议
  - Sec-WebSocket-Version：表示支持的 websocket 版本，默认为 13
  - Sec-Wesocket-Key：是一个 Base64 encode 的值，服务器会用这个值构造出一个 SHA-1 的信息摘要，用于表示同意连接

  客户端请求：

  ```bash
  GET /chat HTTP/1.1
  Host: server.example.com
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
  Origin: http://example.com
  Sec-WebSocket-Protocol: chat, superchat
  Sec-WebSocket-Version: 13
  ```

  服务端返回：

  ```bash
  HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=Sec-WebSocket-Protocol: chat
  ```

### Q2: 优点

- 支持双向通信，实时性更强
- 较少的控制开销：数据包头部协议较小，不同于 http 每次请求需要携带完整的头部
- 保持创连接状态：创建通信后，可省略状态信息，不同于 HTTP 每次请求需要携带身份验证
- 更好的二进制支持：定义了二进制帧，更好处理二进制内容
- 支持扩展：用户可以扩展 websocket 协议、实现部分自定义的子协议
- 更好的压缩效果：Websocket 在适当的扩展支持下，可以沿用之前内容的上下文，在传递类似的数据时，可以显著地提高压缩率

### Q3: 应用场景

基于 websocket 的实时通信的特点

- 聊天室
- 弹幕
- 消息推送
- 多玩家游戏
- 协同编辑
- 直播
- 基于位置的应用
