# HTTP

> HTTP (Hyper Text Transfer Protocol) is the protocol used to exchange information between web-browsers and servers. The  http namespace offers full support for the client side of the HTTP protocol (i.e., the facilities that would be used by a web-browser implementation). The implementation conforms to the HTTP/1.1 standard, RFC 2616.

HTTP(超文本传输协议)是一个用于在 web 浏览器和 server 之间交换信息的协议. http 命名空间提供对于客户端 HTTP 协议的完整支持(例如, 浏览器工具的实现). 实现适用于标准的 HTTP/1.1, RFC 2616.

> The module exports functions that provide HTTP functionality in different levels of abstraction. From the simple string oriented requests, through generic LTN12 based, down to even lower-level if you bother to look through the source code.

模块导出的函数在不同的抽象层次提供HTTP功能。最简单的是使用字符串请求, 还有就是通过通用的基于 LTN12 的方式请求, 甚至可以使用更底层的 api, 如果你费心去看源代码的话.

> To obtain the http namespace, run:

获得 http 命名空间, 运行:

```lua
-- loads the HTTP module and any libraries it requires
-- 加载 HTTP 模块和任何其他所需的
local http = require("socket.http")
```

> URLs must conform to RFC 1738, that is, an URL is a string in the form:

URLs 必须遵照 RFC 1738, 换言之, 一个 URL 是一个如下格式的字符串:

`[http://][<user>[:<password>]@]<host>[:<port>][/<path>]`

> MIME headers are represented as a Lua table in the form:

MIME 头以一个 lua 的表来表示, 如下:

```lua
headers = {
  field-1-name = field-1-value,
  field-2-name = field-2-value,
  field-3-name = field-3-value,
  ...
  field-n-name = field-n-value
}
```

> Field names are case insensitive (as specified by the standard) and all functions work with lowercase field names (but seesocket.headers.canonic). Field values are left unmodified.

字段名是区分大小写的(按标准规定)并且全部的功能基于小写的字段工作(不过可以查看 socket.headers.canonic). 字段值保留不变.

> Note: MIME headers are independent of order. Therefore, there is no problem in representing them in a Lua table.

注意: MIME 头不是有序的. 因此, 使用 lua 的表去表示它们是没有问题的.

> The following constants can be set to control the default behavior of the HTTP module:

下面的常量能够被设置用来控制 HTTP 模块的默认行为:

> * PORT: default port used for connections;
> * PROXY: default proxy used for connections;
> * TIMEOUT: sets the timeout for all I/O operations;
> * USERAGENT: default user agent reported to server.

* PORT: 用于连接的默认端口;
* PROXY: 用于连接的默认 proxy, proxy: 代理服务器, 其功能就是代理网络用户去取得网络信息. 形象的说: 它是网络信息的中转站. 代理服务器是介于浏览器和Web服务器之间的一台服务器, 有了它之后, 浏览器不是直接到 Web 服务器去取回网页而是向代理服务器发出请求, request 信号会先送到代理服务器, 有代理服务器来取回浏览器所需要的信息并传送给你的浏览器;
* TIMEOUT: 设置所有的 I/O 操作超时时间;
* USERAGENT: 默认上报服务器的 agent, agent: 是用来检查浏览页面的访问者在用什么操作系统(包括版本号)浏览器(包括版本号)和用户个人偏好的代码;

```lua
http.request(url [, body])
http.request{
  url = string,
  [sink = LTN12 sink,]
  [method = string,]
  [headers = header-table,]
  [source = LTN12 source],
  [step = LTN12 pump step,]
  [proxy = string,]
  [redirect = boolean,]
  [create = function]
}
```

> The request function has two forms. The simple form downloads a URL using the GET or POST method and is based on strings. The generic form performs any HTTP method and is LTN12 based.

request 函数有两种形式. 简单形式使用 GET 或 POST 方法下载一个 URL, 同时是基于字符串形式的. 通用形式是可以使用 HTTP 的任何方法, 并且是基于 LTN12 的.

> If the first argument of the request function is a string, it should be an url. In that case, if a body is provided as a string, the function will perform a POST method in the url. Otherwise, it performs a GET in the url

如果 request 的第一个参数是 string 类型, 那么它应该是一个 url. 在这种情况下, 如果 body 参数提供一个字符串, 函数将会以当前的 url 执行 POST 方法. 否则, 将会以当前的 url 执行 GET 方法.

> If the first argument is instead a table, the most important fields are the url and the simple LTN12 sink that will receive the downloaded content. Any part of the url can be overridden by including the appropriate field in the request table. If authentication information is provided, the function uses the Basic Authentication Scheme (see  note) to retrieve the document. If sink is nil, the function discards the downloaded data. The optional parameters are the following:

如果第一个参数是 table 类型, url 是非常重要必须要有的字段, sink 字段使用简单的 LTN12 接收下载的内容. url 的任何部分都能够被 request 表对应的字段覆盖. 如果提供了认证信息, 那么函数将使用基本认证方案(查看注意2)检索文档. 如果 sink  字段为 nil, 那么函数将抛弃下载的数据. 可选的一些参数如下所示:

> * method: The HTTP request method. Defaults to "GET";
> * headers: Any additional HTTP headers to send with the request;
> * source: simple LTN12 source to provide the request body. If there is a body, you need to provide an appropriate "content-length" request header field, or the function will attempt to send the body as "chunked" (something few servers support). Defaults to the empty source;
> * step: LTN12 pump step function used to move data. Defaults to the LTN12 pump.step function.
> * proxy: The URL of a proxy server to use. Defaults to no proxy;
> * redirect: Set to false to prevent the function from automatically following 301 or 302 server redirect messages;
> * create: An optional function to be used instead of socket.tcp when the communications socket is created.

* method: HTTP 请求的方法, 默认为 "GET";
* headers: HTTP 请求可以附件的头部信息;
* source: 为请求正文提供的简单 LTN12 source. 如果有请求正文, 你需要提供一个 "content-length" 在请求的 headers 中, 或者函数将尝试把正文作为 "chunked" 发送(只有少数的服务器会支持). 默认 source 为 nil;
* step: LTN12 pump step 函数, 用于移动数据. 默认是 LTN12 pump.step 函数;
* proxy: 代理服务器的 URL. 默认没有使用代理;
* redirect: 设置为 false 将预防服务器的 301 或 302 命令自动重定向消息的功能;关于301和302的解释
* create: 当通信 socket 被创建的时用来替代 socket.tcp 函数的可选函数.

> In case of failure, the function returns nil followed by an error message. If successful, the simple form returns the response body as a string, followed by the response status code, the response headers and the response status line. The generic function returns the same information, except the first return value is just the number 1 (the body goes to the sink).

在失败的情况下, 函数将返回 nil 并且伴随着错误信息. 如果成功, 简单形式将以字符串的方式返回响应, 并且伴随着响应的状态码, 响应的头, 响应的状态行. 通用形式的函数返回相同的信息, 除了第一个参数会返回数字 1 外(正文内容转到 sink 中).

> Even when the server fails to provide the contents of the requested URL (URL not found, for example), it usually returns a message body (a web page informing the URL was not found or some other useless page). To make sure the operation was successful, check the returned status code. For a list of the possible values and their meanings, refer to  RFC 2616.

即使当服务器未能提供请求的网址内容(例如, URL 没有找到), 那么它通常也会返回一个消息主体(一个没有找到该 URL 消息的网页或者一些无效的页面). 如果需要确保操作成功, 请检查返回的状态码. 这些状态码所代表的意义可以参考 RFC 2616.

> Here are a few examples with the simple interface:

这里有一些使用了简单形式接口的例子:

```lua
-- load the http module
-- 加载 http 模块
local io = require("io")
local http = require("socket.http")
local ltn12 = require("ltn12")

-- connect to server "www.cs.princeton.edu" and retrieves this manual
-- file from "~diego/professional/luasocket/http.html" and print it to stdout
-- 连接到服务器 "www.cs.princeton.edu" 并且从 "~diego/professional/luasocket/http.html" 检索本手册文件, 然后将信息打印到 stdout 上.
http.request{
    url = "http://www.cs.princeton.edu/~diego/professional/luasocket/http.html",
    sink = ltn12.sink.file(io.stdout)
}

-- connect to server "www.example.com" and tries to retrieve
-- "/private/index.html". Fails because authentication is needed.
-- 连接到服务器 "www.example.com", 然后检索 "/private/index.html". 会报告失败, 因为需要认证.
b, c, h = http.request("http://www.example.com/private/index.html")
-- b returns some useless page telling about the denied access,
-- h returns authentication information
-- and c returns with value 401 (Authentication Required)
-- b 返回关于访问禁止的无效页面描述,
-- h 返回认证的信息
-- c 返回值为 401(需要认证)

-- tries to connect to server "wrong.host" to retrieve "/"
-- and fails because the host does not exist.
-- 尝试连接到 "wrong.host" 服务器, 并且检索 "/", 然后会失败, 因为主机并不存在.
r, e = http.request("http://wrong.host/")
-- r is nil, and e returns with value "host not found"
```

> And here is an example using the generic interface:

这里有一些使用了通用形式接口的例子:

```lua
-- load the http module
-- 加载 http 模块
http = require("socket.http")

-- Requests information about a document, without downloading it.
-- Useful, for example, if you want to display a download gauge and need
-- to know the size of the document in advance
-- 请求一个文本的信息, 但是不下载它.
-- 用处, 例如, 例如，如果你想显示一个下载表，需要事先知道文件大小.
r, c, h = http.request {
  method = "HEAD",
  url = "http://www.tecgraf.puc-rio.br/~diego"
}

-- r is 1, c is 200, and h would return the following headers:
-- r 为 1, c 为 200, h 将返回以下消息头:
-- h = {
--   date = "Tue, 18 Sep 2001 20:42:21 GMT",
--   server = "Apache/1.3.12 (Unix)  (Red Hat/Linux)",
--   ["last-modified"] = "Wed, 05 Sep 2001 06:11:20 GMT",
--   ["content-length"] = 15652,
--   ["connection"] = "close",
--   ["content-Type"] = "text/html"
-- }
```

> Note: When sending a POST request, simple interface adds a "Content-type: application/x-www-form-urlencoded" header to the request. This is the type used by HTML forms. If you need another type, use the generic interface.

注意1: 当发送 POST 请求时, 简单形式的接口会添加一个 "Content-type: application/x-www-form-urlencoded" 头到请求中. 这种类型常用于 HTML 表单. 如果你需要使用其他的类型, 使用通用接口.

> Note: Some URLs are protected by their servers from anonymous download. For those URLs, the server must receive some sort of authentication along with the request or it will deny download and return status "401 Authentication Required".

注意2: 一些 URLs 是受到服务器匿名下载保护的(防止匿名下载). 对于这些 URLs, 服务器必须接受一些关于请求的身份验证, 或者服务器也会禁止下载并且返回状态 "401 Authentication Required".

> The HTTP/1.1 standard defines two authentication methods: the Basic Authentication Scheme and the Digest Authentication Scheme, both explained in detail in RFC 2068.

HTTP/1.1 标准定义了两种认证方式: 基本身份验证方案和摘要式身份验证方案, 这两个的详细解释在 RFC 2068.

> The Basic Authentication Scheme sends \<user> and \<password> unencrypted to the server and is therefore considered unsafe. Unfortunately, by the time of this implementation, the wide majority of servers and browsers support the Basic Scheme only. Therefore, this is the method used by the toolkit whenever authentication is required.

基本身份验证方案发送不加密的 <用户名> 和 <密码> 到服务器, 因此被认为是不安全的. 不幸的是, 大多数的服务器和浏览器都只支持基本身份验证方案, 因为在使用这个方案时, 都认为认证是必须的.

```lua
-- load required modules
-- 加载需要的模块
http = require("socket.http")
mime = require("mime")

-- Connect to server "www.example.com" and tries to retrieve
-- "/private/index.html", using the provided name and password to
-- authenticate the request
-- 连接到 "www.example.com" 服务器, 并且视图检索 "/private/index.html", 使用提供的用户名和密码认证请求.
b, c, h = http.request("http://fulano:silva@www.example.com/private/index.html")

-- Alternatively, one could fill the appropriate header and authenticate
-- the request directly.
-- 二选一, 另外一种方式是填写合适的消息头, 然后直接认证请求.
r, c = http.request {
  url = "http://www.example.com/private/index.html",
  headers = { authorization = "Basic " .. (mime.b64("fulano:silva")) }
}
```
