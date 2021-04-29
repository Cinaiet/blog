---
title: web协议详解
categories:
  - 大千世界
tags:
  - 花满楼
toc: false
date: 2020-03-29 18:06:22
---

web协议及抓包

<!--more-->

## 评估web架构的关键属性

### HTTP协议应当在以下属性中取得可接受的均衡:

- 性能 performance : 影响高可用的关键因素
- 可伸缩性 scalability : 支持部署可以交互的大量组件
- 简单性 simpilcity : 易理解、易实现、易验证
- 可见性 visiavle : 对两个组件间的交互进行监视或仲裁的能力。如缓存，分层设计等。
- 可移植性 portability : 在不同的环境下运行的能力
- 可靠性 reliablity : 出现部分故障时，对整体的影响程度
- 可修改性 modifiability : 对系统做出修改的难易程度，有可定制化性、可扩展性、可进化性、可配置性、可重用性构成
  - 可进化性: 一个组件独立升级而不影响其它组件
  - 可扩展性: 向系统添加功能而不会影响系统其它部分
  - 可定制型: 临时性、定制性的更改某一要素来提供服务，不对常规客户产生影响
  - 可重用性: 组件可以不做修改在其它应用中使用
  - 可配置性: 应用部署后可通过修改配置提供新的功能

### 架构属性

#### 网络性能

- 吞吐量 : 小于等于服务的带宽
- 开销: 首次开销、每次开销

#### 用户感知到的性能

- 延迟: 发起请求到相应的时间
- 完成时间: 完成一个应用动作所花费的时间

#### 网络效率

- 重用缓存、减少交互次数、数据传输距离更近、COD

### 架构风格

风格 | 简单性 | 可靠性 | 可进化性 | 可扩展性 | 可配置性 | 可重用性 | 可伸缩性 | 网络效率 | 
--- | --- | --- | --- | --- | --- | --- | --- | --- |
数据流风格 | ✔️ | | ✔️| ✔️| ✔️ | ✔️ | | |
复制风格 |用户可察觉的性能 | ✔️| | | | |✔️| ✔️|
分层风格 | ✔️| ✔️ | |  | |  | ✔️ | |
移动代码风格| 可移植性|||✔️||||✔️|
点对点风格 | ||✔️|✔️|✔️|✔️ ||

风格简单性可靠性可进化性可扩展性可配置性可重用性可伸缩性网络效率数据流风格✔️✔️✔️✔️✔️复制风格用户可察觉的性能✔️✔️✔️分层风格✔️✔️✔️移动代码风格可移植性✔️✔️点对点风格✔️✔️✔️✔️#### 数据流风格 Data-flow-style

- 管道与过滤器(或者称为组件)
  每个filter都有输入端和输出端,只能从输入端读取数据,处理后再从输出端产生数据
- 接口统一的管道与过滤器
  如在每个接口都增加统一的约束,则整个架构的简单性会得到增强

#### 复制风格 Replication Style

- 复制仓库
  多个进程提供相同的服务,如通过反向代理对外提供您集中服务
- 缓存
  在客户端或中间的代理中,通过复制请求的结果,为后续的请求复用

#### 分层风格 Hierarchical Styles

- 客户端服务器 Client-Server CS
  - 由Client(客户端)触发请求,Server(服务端)监听到请求后响应,Client一直等待收到响应后,会话结束.
  - 分离关注点隐藏细节,Server只关注资源的生成和管理,Client关注资源的可视化渲染,具有良好的可伸缩性、可进化性、简单性。
- 分层系统 Layered System LS
  - 每一层为其之上的层服务,并使用在其下的层所提供的服务,如TCP,IP
- 分层客户端服务器 Layered-Client-Server LCS
  - 如正向代理和反向代理,从空间上分为外部层和内部曾,正向代理把客户端和网络环境进行分离,反向代理将企业内网与外网进行分层。
- 无状态、客户端服务器 Client-stateless-Server CSS
  - 基于CS,服务器上不允许有session,state等会话状态
  - 提升了可伸缩性、可见性、可靠性、但重复数据降低了网络的性能
- 缓存、无状态客户端服务器 Client-Cacha-Stateless Server C$SS
  - 提升性能
- 分层、缓存、无状态客户端服务器 Layered-Client-Cacha-Stateless Server LC$SS

#### 移动代码风格 Mobile code style

- 虚拟机 Virtual Machine, VM
  分离指令与实现
- 远程求值 Remote Evaluation REV
  基于CS的VM,将代码发送至服务器运行
- 按需代码 Code on Demand COD
  - 服务器在响应中发回处理代码，在客户端执行
  - 优秀的可扩展性和可移植性，提升用户可察觉性性能和网络效率
- 分层、按需代码、缓存、无状态客户端服务器 LCODC$SS
- 移动代理 Mobile Agent MA
  相当于 REV +COD

#### 点对点风格 Peer-to-Peer style

- Event-Based Integration EBI
  - 基于事件集成系统，如消息的订阅，消费。
  - 优秀的可重用性、可扩展性、可进化性
  - 缺乏可理解性
  - 由于消息广播等因素造成的消息风暴,可伸缩性差
- Chiorn2 C2
  相当于 EBI + LCS 控制了消息的方向
- Distributed Objects DO
  组件结对交互
- Brokered Distributed Objects BDO
  引入名字来解析组件来简化DO，例如CORBA

---

## HTTP的前世今生

### HTTP 是什么

**超文本传输协议**

重点在于P(Protocol)，用于约定服务端与客户端 超文本(当前已有图片、音频超链接等)数据传递交互的规范。

#### 与HTTP相关的概念

##### CDN

内容分发器。应用了HTTP协议的缓存和代理技术，代替源站去相应客户端的请求。
简单来说，就是缓存了源网站的数据，客户端再次请求时，可以不用去源服务器去请求，降低响应时间。

##### Web Service

由W3C定义的应用服务开发规范，有client-server 主从架构，通过使用WSDL定义服务接口，使用HTTP传输xml或SOAP消息，也就是说，它是一个**基于Web(HTTP)的服务架构技术**。

##### TCP/IP

TCP/IP 协议实际上是一系列网络通信协议的统称，其中最核心的两个协议是 TCP 和 IP，其他的还有 UDP、ICMP、ARP 等等，共同构成了一个复杂但有层次的协议栈。

- TCP协议 属于**传输层** 传输控制协议。位于IP协议之上，基于IP协议提供 <font color=green>可靠的</font>、<font color=green>字节流</font>形式的通信。是HTTP协议实现的基础。
  TCP是一个有状态的协议，需要先与对方建立连接，然后才能发送数据，并且保证数据不丢失不重复。TCP的数据时连续的字节流，有先后顺序。
- IP 协议 属于**网际层** 主要目的是解决寻址和路由问题，以及如何在两点之间传递数据包。
- TCP/IP协议总共有四层
  
  1. 链接层  负责在以太网、WIFI这样的底层发送原始数据包，工作在网卡这个层次，使用MAC地址来标记网络上的设备，也叫MAC层。传输单位是帧。
  2. 网际层(网络互连层) IP协议就在这一层。把IP地址转换成MAC地址。传输单位是包。
  3. 传输层 保证数据在IP地址标记的两点之间“可靠的”传输，是TCP/IP协议工作的层次。传输单位是段。
  4. 应用层

##### DNS

域名系统 是IP地址的等价替代，用域名解析实现IP地址的映射。
如 [薄梦风轻棉](blog.wangguanwei.com)

##### URI/URL

### HTTP/0.9

只允许使用GET从服务端获取HTML文档，并在请求响应之后立即关闭连接。

### HTTP/1.0

- 增加了HEAD、POST等方法；
- 增加了相应状态码，标记可能出现的错误原因；
- 引入协议版本号的概念；
- 引入了HTTP Header(头部)的概念，让HTTP处理请求和相应更加灵活；
- 传输的数据不再局限于文本。

### HTTP/1.1

- 增加了PUT、DELETE新方法；
- 增加缓存管理和控制；
- 明确连接管理，允许持久连接；
- 允许相应数据分块(chunked)，利于传输大文件；
- 强制要求Host头，让互联网主机托管称为可能。

### HTTP/2

**HTTP/2 基于 Google 的 SPDY 协议，注重性能改善，但还未普及；**

- 二进制协议，不再是纯文本；
- 可发起多个请求，废弃1.1中的管道；
- 使用专用算法压缩头部，减少数据传输量；
- 允许服务器主动向客户端推送数据；
- 要求加密通信，增加了安全性。

### HTTP/3

**HTTP/3 基于 Google 的 QUIC 协议，是将来的发展方向。**

---

## HTTP1详解

### request-line 请求行

request-line = method + request-target + http-version CRLF

#### method (方式)

**幂等： 多次执行相同的操作，结果也都是相同的，即多次“幂”后结果“相等”**

指明请求方式

- GET
  主要的获取数据的方法，大量的性能优化都针对该方法，也是幂等方法(调用一次和调用多次获得的结果是完全一致的)
- POST
  主要用于向服务器提交数据(资源)。常用于form表单提交、新增资源等。POST有新建(credit)的含义。
- HEAD
  类似于GET方法，但服务器不会返回请求的实体数据，只会传回响应头，也就是元数据。幂等方法
- PUT
  替换或更新数据，更新资源时使用。带条件时是幂等方法（多次更新一个资源，资源还是第一次更新的状态。）PUT有修改(update)的含义。
- DELETE
  删除资源，因为该动作危险性大通常服务器不会执行真正的删除操作，而是对资源做一个删除的标记。当然更多的服务器直接不处理该方法。幂等方法（可以多次删除同一个资源，得到的结果都是“资源不存在”。）
- CONNECT
  建立tunnel隧道。要求服务器为客户端和另一台远程服务器建立一条特殊的连接隧道，这时 Web 服务器在中间充当了代理的角色。
- OPTIONS
  显示服务器对访问资源支持的方法，在响应头的ALLOW字段中返回。幂等方法

```
$ curl static.taohui.tech -X OPTIONS -I
// Allow列举出支持的方式

HTTP/1.1 200 OK
Server: openresty/1.15.8.1
Date: Wed, 01 Apr 2020 15:27:15 GMT
Content-Length: 0
Connection: keep-alive
DAV: 2
Allow: GET,HEAD,PUT,DELETE,MKCOL,COPY,MOVE,PROPFIND,OPTIONS,LOCK,UNLOCK
```

- TRACE
  用于对HTTP链路或诊断，可以显示出请求-相应的传输路径。它的本意是好的，但存在漏洞，会泄漏网站的信息，所以 Web 服务器通常也是禁止使用。

#### request-target (Path)

request-target有四种类型。origin-form、absolute-form、authority-form、asterisk-form。

- origin-form
  向语言服务器，及产生响应内容的服务器发送请求的方式
- absolute-form
  仅用于向正向代理发送请求时。后面需要跟完整的URI
- authority-form
  仅用于Connect方法，及需要建立VPN隧道时使用
- asterisk-form
  仅用于Option方法，可以在request-target中传 "*" 号。

#### http-version 版本号

- HTTP/0.9
  只支持GET方法，已过时
- HTTP/1.0
  常适用于代理服务器中，如Nginx向上游发起连接时，默认还是使用的1.0 的协议
- HTTP/1.1
  对1.0做出的改进，支持缓存、超链接，通过Host对于域名的支持。
- HTTP/2.0

#### 用于文档管理的WEBDAV方法

> WebDAV是用于Web协作创作的Internet工程任务组（IETF）标准：超文本传输​​协议（HTTP）的一组扩展，便于远程用户之间的协作编辑和文件管理在互联网上互相访问。

- PROPFIND 从web资源中检索以XML格式存储的属性，。它也被重载，以允许一个检索远程系统的集合结构(也叫目录层次结构)，如文件中切换目录
- PROPPATCH 在单个原子性动作中更改和删除资源的多个属性。
- MKCOL 创建集合或目录
- COPY 将资源从一个URI复制到另一个URI
- MOVE 将资源从一个URI移动到另一个URI
- LOCK 锁定一个资源。WEBDAV支持共享锁和互斥锁
- UNLOCK 解除资源的锁定

### status-line 响应行

status-line = HTTP-version + status-code + reason-pharse CRLF

#### status-code 响应码

##### 1xx : 请求已经接收到，需要进一步处理才能完成，HTTP1.0不支持

- 100 Continue: 上传大文件前使用。(由客户端发起请求中携带 Expect: 100-continue 头部触发)
- 101 Switch Protocols : 协议升级使用。(由客户端发起请求中携带Upgrade: 头部触发，如升级websocket或http2.0
- 102 Processing : WEBDAV请求可能会包含许多涉及文件操作的子请求，需要很长时间才能完成请求。该代码表示服务器已经接收到并正在处理请求，但无响应可用，这样可以防止客户端超时，并假设请求丢失。

##### 2xx: 成功处理请求

- 200 OK :  成功返回响应
- 201 Created : 由新资源在服务器端被成功创建
- 202 Accepted : 服务器接收并开始处理请求,但请求未处理完成。如异步、需要长时间处理的任务。
- 203 NON-Authoritative Information : 当代理服务器修改了 origin server 的原始响应包体时(例如更换了HTML中的元素值)，代理服务器可以通过修改200为203的方式告知客户端这一事实，方便客户端做出相应的处理。203也可被缓存。
- 204 No Content : 成功执行了请求且不携带请求包体,并暗示客户端无需更新当前页面视图。
- 205 Reset Content:成功执行了请求且不携带响应包体，同时指明客户端 需要更新当前页面视图。
- 206 Partial Content:使用 range 协议时返回部分响应内容时的响应码
- 207 Multi-Status:RFC4918 ，在 WEBDAV 协议中以 XML 返回多个资源的状态。
- 208 Already Reported:RFC5842 ，为避免相同集合下资源在207响应码 下重复上报，使用 208 可以使用父集合的响应码。

##### 3xx: 重定向

使用 Location 指向的资源或者缓存中的资源。在 RFC2068 中规定客户端重定向次数不应超过 5 次，以防止死循环。

- 300 Multiple Choices:资源有多种表述，通过 300 返回给客户端后由其 自行选择访问哪一种表述。由于缺乏明确的细节，300 很少使用。
- 301 Moved Permanently:资源永久性的重定向到另一个 URI 中。
- 302 Found:资源临时的重定向到另一个 URI 中。
- 303 See Other:重定向到其他资源，常用于 POST/PUT 等方法的响应中。
- 304 Not Modified:当客户端拥有可能过期的缓存时，会携带缓存的标识 etag、时间等信息询问服务器缓存是否仍可复用，而304是告诉客户端可以 复用缓存。
- 307 Temporary Redirect:类似302，但明确重定向后请求方法必须与原 请求方法相同，不得改变。
- 308 Permanent Redirect:类似301，但明确重定向后请求方法必须与原请 求方法相同，不得改变。

##### 4xx: 客户端出现错误

- 400 Bad Request:服务器认为客户端出现了错误，但不能明确判断为以下哪种错误时使用此错误码。例如HTTP请求格式错误。
- 401 Unauthorized:用户认证信息缺失或者不正确，导致服务器无法处理请求。
- 407 Proxy Authentication Required:对需要经由代理的请求，认证信息未通过代理服务器的验证
- 403 Forbidden:服务器理解请求的含义，但没有权限执行此请求
- 404 Not Found:服务器没有找到对应的资源
- 410 Gone:服务器没有找到对应的资源，且明确的知道该位置永久性找不到该资源
- 405 Method Not Allowed:服务器不支持请求行中的 method 方法
- 406 Not Acceptable:对客户端指定的资源表述不存在(例如对语言或者编码有要求)，服务器返回表述列表供客户端选择。
- 408 Request Timeout:服务器接收请求超时
- 409 Conflict:资源冲突，例如上传文件时目标位置已经存在版本更新的资源
- 411 Length Required:如果请求含有包体且未携带 Content-Length 头部，且不属于 chunk类请求时，返回 411
- 412 Precondition Failed:复用缓存时传递的 If-Unmodified-Since 或 If- None-Match 头部不被满足
- 413 Payload Too Large/Request Entity Too Large:请求的包体超出服务器能处理的最大长度
- 414 URI Too Long:请求的 URI 超出服务器能接受的最大长度
- 415 Unsupported Media Type:上传的文件类型不被服务器支持
- 416 Range Not Satisfiable:无法提供 Range 请求中指定的那段包体
- 417 Expectation Failed:对于 Expect 请求头部期待的情况无法满足时的响应码
- 421 Misdirected Request:服务器认为这个请求不该发给它，因为它没有能力处理。
- 426 Upgrade Required:服务器拒绝基于当前 HTTP 协议提供服务，通过 Upgrade 头部告知客户端必须升级协议才能继续处理。
- 428 Precondition Required:用户请求中缺失了条件类头部，例如 If-Match
- 429 Too Many Requests:客户端发送请求的速率过快
- 431 Request Header Fields Too Large:请求的 HEADER 头部大小超过限制
- 451 Unavailable For Legal Reasons:RFC7725 ，由于法律原因资源不可访问

##### 5xx:服务器端出现错误

- 500 Internal Server Error:服务器内部错误，且不属于以下错误类型
- 501 Not Implemented:服务器不支持实现请求所需要的功能
- 502 Bad Gateway:代理服务器无法获取到合法响应
- 503 Service Unavailable:服务器资源尚未准备好处理当前请求
- 504 Gateway Timeout:代理服务器无法及时的从上游获得响应
- 505 HTTP Version Not Supported:请求使用的 HTTP 协议版本不支持
- 507 Insufficient Storage:服务器没有足够的空间处理请求
- 508 Loop Detected:访问资源时检测到循环
- 511 Network Authentication Required:代理服务器发现客户端需要进 行身份验证才能获得网络访问权限

### Host 头部

Host = uri-host [ ":" port ]

- HTTP/1.1 规范要求，不传递 Host 头部则返回 400 错误响应码
- 为防止陈旧的代理服务器，发向正向代理的请求 request-target 必须以 absolute-form 形式出现

### Request Headers 请求的上下文

#### User-Agent

指明客户端的类型信息，服务器可以据此对资源的表述做抉择
User-Agent = product *( RWS ( product / comment ) )

- product = token ["/" product-version]
  包含两部分，一部分为token，一部分为描述token指向的软件的版本号
  例: User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:66.0)
  Gecko/20100101 Firefox/66.0

#### referer

浏览器对来自某一页面的请求自动添加的头部
Referer = absolute-URI / partial-URI
例: Referer: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/User-Agent

Referer 不会被添加的场景:

1. 来源页面采用的协议为表示本地文件的 "file" 或者 "data" URI
2. 当前请求页面采用的是 http 协议，而来源页面采用的是 https 协议

服务器端常用于统计分析、缓存优化、防盗链等功能

#### From

主要用于网络爬虫，告诉服务器如何通过邮件联系到爬虫的负责人
From = mailbox
例如: From: webmaster@example.org

### Response Headers 响应的上下文

#### server

Server = product *( RWS ( product / comment ) )
指明服务器上所用软件的信息，用于帮助客户端定位问题或者统计数据
product = token ["/" product-version]

#### Allow

告诉客户端，服务器上该 URI 对应的资源允许哪些方法的执行
Allow = #method

#### Accept-Ranges

告诉客户端服务器上该资源是否允许 range 请求
Accept-Ranges = acceptable-ranges

### HTTP包体

承载的消息内容

- 请求或者响应都可以携带包体
  HTTP-message = start-line *( header-field CRLF ) CRLF [ message-body ]
  message-body = *OCTET:二进制字节流
- 以下消息不能含有包体
  1. HEAD 方法请求对应的响应
  2. 1xx、204、304 对应的响应
  3. CONNECT 方法对应的 2xx 响应

#### 发送 HTTP 消息时已能够确定包体的全部长度

使用 Content-Length 头部明确指明包体长度
Content-Length = 1*DIGIT
用 10 进制(不是 16 进制)表示包体中的字节个数，且必须与实际传输的包体长度一致
优点:接收端处理更简单

#### 发送 HTTP 消息时不能确定包体的全部长度

使用 Transfer-Encoding 头部指明使用 Chunk 传输方式，
含 Transfer-Encoding 头部后 Content-Length 头部应被忽略
优点:

1. 基于长连接持续推送动态内容
2. 压缩体积较大的包体时，不必完全压缩完(计算出头部)再发送，可以边发送边压缩
3. 传递必须在包体传输完才能计算出的 Trailer 头部

### HTML Form表单

表单，HTML 中的元素，提供了交互控制元件用来向服务器通过 HTTP 协议提交信息。

#### 关键属性

- action:提交时发起 HTTP 请求的 URI
- method:提交时发起 HTTP 请求的 http 方法
  - GET:通过 URI，将表单数据以 URI 参数的方式提交
  - POST:将表单数据放在请求包体中提交
- enctype:在 POST 方法下，对表单内容在请求包体中的编码方式
  - application/x-www-form-urlencoded
    数据被编码成以 ‘&’ 分隔的键-值对, 同时以 ‘=’ 分隔键和值，字符以 URL 编码方式编码
  - multipart/form-data
    boundary 分隔符
    每部分表述皆有HTTP头部描述子包体，例如 Content-Type
    last boundary 结尾
- Content-type 头部指明这是一个多表述包体
  Content-type: multipart/form-data
- Boundary 分隔符的格式

### Cookie

保存在客户端、由浏览器维护、表示应 用状态的 HTTP 头部。

- 存放在内存或者磁盘中
- 服务器端生成 Cookie 在响应中通过 Set-Cookie 头部告知客户端(允许多 个 Set-Cookie 头部传递多个值)
- 客户端得到 Cookie 后，后续请求都会 自动将 Cookie 头部携带至请求中
  Cookie 头部中可以存放多个 name/value 名值对
  Set-Cookie 头部一次只能传递 1 个 name/value 名值对，响应中可以含多个头部

#### Cookie的使用限制

- 每条 Cookie 的长度(包括 name、value 以及描述的属性等总长度)至于要达到 4KB • 每个域名下至少支持 50 个 Cookie
- 至少要支持 3000 个 Cookie
- 代理服务器传递 Cookie 时会有限制

#### Cookie在协议设计上的问题

- Cookie 会被附加在每个 HTTP 请求中，所以无形中增加了流量
- 由于在 HTTP 请求中的 Cookie 是明文传递的，所以安全性成问题(除
  非用 HTTPS)
- Cookie 的大小不应超过 4KB，故对于复杂的存储需求来说是不够用的

### 同源策略

同一个浏览器发出的请求，未必都是用户自愿发出的请求。如果没有同源策略，只能保证用户请求来自于同一浏览器，不能确保是用户自愿发出的。

#### 解决跨域问题

详见文章 [解决跨域的几种方式](https://blog.wangguanwei.com/2017/07/23/%E8%A7%A3%E5%86%B3%E8%B7%A8%E5%9F%9F%E9%97%AE%E9%A2%98%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E5%BC%8F/),本文不再一一赘述。

### Cache-Control 头部

请求中的头部:max-age、max-stale、min-fresh、no-cache、no- store、no-transform、only-if-cached
响应中的头部: max-age、s-maxage 、 must-revalidate 、proxy- revalidate 、no-cache、no-store、no-transform、public、private

#### Cache-Control 头部在请求中的值

- max-age:告诉服务器，客户端不会接受 Age 超出 max-age 秒的缓存
- max-stale:告诉服务器，即使缓存不再新鲜，但陈旧秒数没有超出 max-stale 时，客户端仍
  打算使用。若 max-stale 后没有值，则表示无论过期多久客户端都可使用
- min-fresh:告诉服务器，Age 至少经过 min-fresh 秒后缓存才可使用
- no-cache:告诉服务器，不能直接使用已有缓存作为响应返回，除非带着缓存条件到上游服 务端得到 304 验证返回码才可使用现有缓存
- no-store:告诉各代理服务器不要对该请求的响应缓存(实际有不少不遵守该规定的代理服务 器)
- no-transform:告诉代理服务器不要修改消息包体的内容
- only-if-cached:告诉服务器仅能返回缓存的响应，否则若没有缓存则返回 504 错误码

#### Cache-Control 头部在响应中的值

- must-revalidate:告诉客户端一旦缓存过期，必须向服务器验证后才可使用
- proxy-revalidate:与 must-revalidate 类似，但它仅对代理服务器的共享缓存
  有效
- no-cache:告诉客户端不能直接使用缓存的响应，使用前必须在源服务器验证 得到 304 返回码。如果 no-cache 后指定头部，则若客户端的后续请求及响应 中不含有这些头则可直接使用缓存
- max-age:告诉客户端缓存 Age 超出 max-age 秒后则缓存过期
- s-maxage:与 max-age 相似，但仅针对共享缓存，且优先级高于 max-age 和 Expires
- public:表示无论私有缓存或者共享缓存，皆可将该响应缓存
- private:表示该响应不能被代理服务器作为共享缓存使用。若 private 后指定头
  部，则在告诉代理服务器不能缓存指定的头部，但可缓存其他部分
- no-store:告诉所有下游节点不能对响应进行缓存
- no-transform:告诉代理服务器不能修改消息包体的内容

## TSL 协议

### AES对称加密

#### 对称加密实现原理

对称机密之所以能实现同一把密钥既能加密又能解密，主要是基于XOR异或运算。

input |input |  output
---- | -- |  -- |
A | B | A XOR B|
0 | 0 | 0
0 | 1 | 1
1 | 0 | 1
1 | 1 | 0

inputinputoutputABA XOR B000011101110inputinputoutputABA XOR B000011101110inputinputoutputABA XOR B000011101110inputinputoutputABA XOR B000011101110#### padding填充

Block cipher 分组加密: 将明文分成多个等长的 Block 模块，对每个模块分别加解密。
目的: 当最后一个明文 Block 模块长度不足时，需要填充。
填充方法:

- 位填充: 以 bit 位为单位来填充
- 字节填充:以字节为单位为填充。有四种方法
  1. 补零: 字节位数不够，全部用零来补。
  2. ANSIX9.23: 末尾补零，最后一个字节表明补了几个零。
  3. ISO10126: 末尾填充完全随机字符，填充的最后一个字节表明一共填充了几个字节
  4. PKCS7(RFC5652): 在需要填充的地方，全部填充数字，数字为需要几个字节。如需要填充4个字节，那么末尾补充 04040404 |.

