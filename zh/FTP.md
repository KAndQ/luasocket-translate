# FTP

> FTP (File Transfer Protocol) is a protocol used to transfer files between hosts. The ftp namespace offers thorough support to FTP, under a simple interface. The implementation conforms to RFC 959.

FTP(文件传输协议)用于在两台主机之间传输文件的协议. ftp 命名空间以简单的接口对 FTP 彻底的支持. 实现符合 RFC 959.

> High level functions are provided supporting the most common operations. These high level functions are implemented on top of a lower level interface. Using the low-level interface, users can easily create their own functions to access any operation supported by the FTP protocol. For that, check the implementation.

高级功能提供支持最常见的操作. 这些高级别的功能是在一个较低的水平接口上实现的. 用低级的接口，用户可以轻松地创建自己的函数来访问的FTP协议支持的任何操作. 为此, 查看具体的实现.

> To really benefit from this module, a good understanding of LTN012, Filters sources and sinks is necessary.

想要得到这个模块真正的福利, 有必要了解一下 LTN012, Filters sources and sinks.

> To obtain the ftp namespace, run:

获得 ftp 命名空间运行以下代码:

```lua
-- loads the FTP module and any libraries it requires
-- 加载 FTP 模块和任何它需要的库.
local ftp = require("socket.ftp")
```

> URLs MUST conform to RFC 1738, that is, an URL is a string in the form:

URLs 必须符合 RFC 1738 格式, 也就是说, 一个 URL 是下列格式的字符串:

`[ftp://][\<user>[:\<password>]@]\<host>[:\<port>][/\<path>][type=a|i]`

> The following constants in the namespace can be set to control the default behavior of the FTP module:

下列在命名空间中的常量能够用来控制 FTP 模块的默认行为:

> * PASSWORD: default anonymous password.
> * PORT: default port used for the control connection;
> * TIMEOUT: sets the timeout for all I/O operations;
> * USER: default anonymous user;

* PASSWORD: 默认匿名密码. (匿名用户 anonymous, 密码随便填一个email地址);
* PORT: 默认控制连接的端口;
* TIMEOUT: 设置所有的 I/O 超时操作;
* USER: 默认匿名用户;

```lua
ftp.get(url)
ftp.get({
  host = string,
  sink = LTN12 sink,
  argument or path = string,
  [user = string,]
  [password = string]
  [command = string,]
  [port = number,]
  [type = string,]
  [step = LTN12 pump step,]
  [create = function]
})
```

> The get function has two forms. The simple form has fixed functionality: it downloads the contents of a URL and returns it as a string. The generic form allows a lot more control, as explained below.

get 函数有两种形式. 简单的形式是固定的功能: 它下载 URL 的内容, 并且将它作为作为字符串返回. 通用形式允许更多的控制, 如下解释.

> If the argument of the get function is a table, the function expects at least the fields host, sink, and one of argument or path (argument takes precedence). Host is the server to connect to. Sink is the simple LTN12 sink that will receive the downloaded data. Argument or path give the target path to the resource in the server. The optional arguments are the following:

如果 get 函数的传入的参数是一个 table 类型, 至少需要以下字段, host, sink 和 argument 或者 path 中的一个(argument 拥有更高的优先级). host 是连接的服务器. sink 是简单的 LTN12 sink, 它会接收下载数据. argument 或者 path 是资源在服务器的目标路径. 可选参数如下:

> * user, password: User name and password used for authentication. Defaults to "ftp:anonymous@anonymous.org";
> * command: The FTP command used to obtain data. Defaults to "retr", but see example below;
> * port: The port to used for the control connection. Defaults to 21;
> * type: The transfer mode. Can take values "i" or "a". Defaults to whatever is the server default;
> * step: LTN12 pump step function used to pass data from the server to the sink. Defaults to the LTN12 pump.step function;
> * create: An optional function to be used instead of socket.tcp when the communications socket is created.

* user, password: 用于认证的用户名和密码. 默认的是"ftp:anonymous@anonymous.org";
* command: 用于获取数据的 FTP 命令. 默认是 "retr", 不过可以参考下面的例子;
* port: 用于控制连接的端口. 默认是 21.
* type: 传输模式. 可用的值是 "i" or "a". 默认值与服务器的默认值相同;
* step: LTN12 pump step 函数, 用于从服务器传输数据到 sink. 默认是 LTN12 pump.step 函数;
* create: 这是可选的函数, 当通信的 socket 创建时用于代替 socket.tcp 函数的方法;

> If successful, the simple version returns the URL contents as a string, and the generic function returns 1. In case of error, both functions return nil and an error message describing the error.

如果成功, 简单版本以字符串的形式返回 URL 内容, 通用模式函数返回 1. 在发生错误的情况下, 两个函数都返回 nil 并且伴随错误的描述信息.

* simple:

```lua
-- load the ftp support
-- 加载支持的 ftp 模块
local ftp = require("socket.ftp")

-- Log as user "anonymous" on server "ftp.tecgraf.puc-rio.br",
-- and get file "lua.tar.gz" from directory "pub/lua" as binary.
-- 以匿名的方式登录 "ftp.tecgraf.puc-rio.br” 服务器, 并且从 "pub/lua” 路径得到文件 “lua.tar.gz".
f, e = ftp.get("ftp://ftp.tecgraf.puc-rio.br/pub/lua/lua.tar.gz;type=i")
```

* generic:

```lua
-- load needed modules
-- 加载需要的模块
local ftp = require("socket.ftp")
local ltn12 = require("ltn12")
local url = require("socket.url")

-- a function that returns a directory listing
-- 返回一个目录列表函数
function nlst(u)
    local t = {}
    local p = url.parse(u)
    p.command = "nlst"
    p.sink = ltn12.sink.table(t)
    local r, e = ftp.get(p)
    return r and table.concat(t), e
end

ftp.put(url, content)
ftp.put{
  host = string,
  source = LTN12 sink,
  argument or path = string,
  [user = string,]
  [password = string]
  [command = string,]
  [port = number,]
  [type = string,]
  [step = LTN12 pump step,]
  [create = function]
}
```

> The put function has two forms. The simple form has fixed functionality: it uploads a string of content into a URL. The generic form allows a lotmore control, as explained below.

put 函数有两种形式. 简单形式只有固定的功能: 它上传一个字符串内容的到 URL. 通用形式允许更多的形式, 如下所示.

> If the argument of the put function is a table, the function expects at least the fields host, source, and one of argument or path (argument takes precedence). Host is the server to connect to. Source is the simple LTN12 source that will provide the contents to be uploaded. Argument or path give the target path to the resource in the server. The optional arguments are the following:

如果传入 put 函数的是 table 类型, 函数期望至少有这些字段, host, source 和 argument 或 path 其中之一(argument 的优先级更高). host 是连接的主机. source 是简单的 LTN12 的 source, 将用于提供上传数据. argument 或者 path 是资源在服务器的目标路径. 以下是可选的参数:

> * user, password: User name and password used for authentication. Defaults to "ftp:anonymous@anonymous.org";
> * command: The FTP command used to send data. Defaults to "stor", but see example below;
> * port: The port to used for the control connection. Defaults to 21;
> * type: The transfer mode. Can take values "i" or "a". Defaults to whatever is the server default;
> * step: LTN12 pump step function used to pass data from the server to the sink. Defaults to the LTN12 pump.step function;
> * create: An optional function to be used instead of socket.tcp when the communications socket is created.

* user, password: 认证的用户名和密码. 默认是 "ftp:anonymous@anonymous.org";
* command: 用于发送数据的 FTP 命令. 默认是 "stor", 不过可以查看下面的例子;
* port: 控制连接的端口. 默认是 21;
* type: 传输模式. 能够取的值是 "i" or "a". 默认值随服务器的默认值而定;
* step: LTN12 pump step 函数, 用于从服务器传输数据到 sink. 默认是 LTN12 pump.step 函数;
* create: 在通信创建时, 用于代替 socket.tcp 的可选函数.
* Both functions return 1 if successful, or nil and an error message describing the reason for failure.
* 如果成功, 两个函数都返回 1, 否则 nil 和描述错误产生原因的错误信息.

* simple:

```lua
-- load the ftp support
-- 加载需要的模块
local ftp = require("socket.ftp")

-- Log as user "fulano" on server "ftp.example.com",
-- using password "silva", and store a file "README" with contents
-- "wrong password, of course"
-- 使用用户 “fulano” 和密码 “silva" 登录 “ftp.example.com” 服务器, 使用文本 "wrong password, of course” 存储到服务器的 “README” 文件中.
f, e = ftp.put("ftp://fulano:silva@ftp.example.com/README", "wrong password, of course")
```

* generic:

```lua
-- load the ftp support
-- 加载 ftp 支持模块
local ftp = require("socket.ftp")
local ltn12 = require("ltn12")

-- Log as user "fulano" on server "ftp.example.com",
-- using password "silva", and append to the remote file "LOG", sending the
-- contents of the local file "LOCAL-LOG"
-- 使用用户 “fulano” 和密码 “silva" 登录 “ftp.example.com” 服务器, 将本地 “LOCAL-LOG” 的内容追加存储到服务器的 “LOG” 文件中.
f, e = ftp.put{
  host = "ftp.example.com",
  user = "fulano",
  password = "silva",
  command = "appe",
  argument = "LOG",
  source = ltn12.source.file(io.open("LOCAL-LOG", "r"))
}
```
