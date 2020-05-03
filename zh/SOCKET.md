# Socket

> The socket namespace contains the core functionality of LuaSocket.

socket 命名空间包括 LuaSocket 的核心功能.

> To obtain the socket namespace, run:

运行以下代码获得 socket 命名空间:

```lua
-- loads the socket module
local socket = require("socket")
```

## socket.bind(address, port [, backlog])

> This function is a shortcut that creates and returns a TCP server object bound to a local address and port, ready to accept client connections. Optionally, user can also specify the backlog argument to the listen method (defaults to 32).

这个函数是一个创建并且返回绑定一个本地地址和端口的 TCP server 对象, 装备着接受客户端的连接. 视情况需要, 用户能够给 listen 方法指定 backlog 参数(默认是 32).

> Note: The server object returned will have the option "reuseaddr" set to true.

注意: 返回 server 对象的 reuseaddr 选项为 true.

## socket.connect[46](address, port [, locaddr] [, locport] [, family])

> This function is a shortcut that creates and returns a TCP client object connected to a remote address at a given port. Optionally, the user can also specify the local address and port to bind (locaddr and locport), or restrict the socket family to "inet" or "inet6". Without specifying family to connect, whether a tcp or tcp6 connection is created depends on your system configuration. Two variations of connect are defined as simple helper functions that restrict the family, socket.connect4 and socket.connect6.

这个函数能够创建并且返回连接到远程终端的 TCP client 对象. 视情况需要, 用户可以绑定指定的本地地址和端口(locaddr 和 locport), 或者限定 socket 的协议族为 "inet" or "inet6". 如果没有指定 family 参数连接, 那么会根据系统的配置创建一个 tcp 或 tcp6. 定义了两种简单的辅助函数限定使用的协议族, socket.coonect4 和 socket.connect6.

## socket._DEBUG

> This constant is set to true if the library was compiled with debug support.

如果编译的是否使用了 debug, 那么常量设置为 true.

## socket.gettime()

> Returns the time in seconds, relative to the origin of the universe. You should subtract the values returned by this function to get meaningful values.

返回时间, 以秒为单位. 该时间表示原始的全局时间, 如果只是单独的调用这个函数得到的值是没有意义的. 你应该通过这个函数的返回值减去之前的值, 得到的差值才是有意义的(间隔时间).

```lua
t = socket.gettime()
-- do stuff
print(socket.gettime() - t .. " seconds elapsed")
```

## socket.headers.canonic

> The socket.headers.canonic table is used by the HTTP and SMTP modules to translate from lowercase field names back into their canonic capitalization. When a lowercase field name exists as a key in this table, the associated value is substituted in whenever the field name is sent out.

socket.headers.canonic 表用于 HTTP 和 SMTP 模块, 这些模块将小写的字段名翻译回它们的标准正文. 当一个小写的字段名作为一个键存在这个表中时, 无论该字段名(键)何时被发送, 都将使用关联的值取而代之.

> You can obtain the headers namespace if case run-time modifications are required by running:

你能够使用下面的代码获得 headers 命名空间, 同时也支持在运行时修改:

```lua
-- loads the headers module
local headers = require("headers")

```

## socket.newtry(finalizer)

> Creates and returns a clean try function that allows for cleanup before the exception is raised.

创建并且返回一个干净的 try 函数, 它会清除之前生成的异常.

> Finalizer is a function that will be called before try throws the exception. It will be called in protected mode.

Finalizer 是一个函数, 将在 try 函数抛出异常前回调. 它将被称为保护模式.

> The function returns your customized try function.

这个函数返回一个自定义的 try 函数.

> Note: This idea saved a lot of work with the implementation of protocols in LuaSocket:

注意: 这个概念为在 LuaSocket 中协议的实现节省了很多的工作.

```lua
foo = socket.protect(function()
    -- connect somewhere
    -- 连接到某个地方
    local c = socket.try(socket.connect("somewhere", 42))

    -- create a try function that closes 'c' on error
    -- 对关闭 ‘c’ 会发生的错误, 创建一个 try 函数
    local try = socket.newtry(function() c:close() end)

    -- do everything reassured c will be closed
    -- 做一些事情, 保证 c 将会被关闭
    try(c:send("hello there?\r\n"))
    local answer = try(c:receive())
    ...
    try(c:send("good bye\r\n"))
    c:close()
end)
```

## socket.protect(func)

> Converts a function that throws exceptions into a safe function. This function only catches exceptions thrown by the try and newtry functions. It does not catch normal Lua errors.

将一个抛出的错误扔到另外一个安全的函数中. 这个函数只捕获 try 和 newtry 函数抛出的异常.它不捕获普通的 Lua 错误.

> Func is a function that calls try (or assert, or error) to throw exceptions.

Func 是一个函数, 它会调用 try(or assert, or error) 这些函数来抛出异常.

> Returns an equivalent function that instead of throwing exceptions, returns nil followed by an error message.

返回一个等价的函数, 代替抛出的异常, 返回 nil 和 一个错误信息.

> Note: Beware that if your function performs some illegal operation that raises an error, the protected function will catch the error and return it as a string. This is because the try function uses errors as the mechanism to throw exceptions.

注意: 提防如果你的函数在执行一些合法操作时抛出异常, 保护函数将捕获错误, 并且将这个错误以字符串的方式返回. 这是因为 try 函数使用的是错误抛出异常的机制.

## socket.select(recvt, sendt [, timeout])

> Waits for a number of sockets to change status.

等待若干个 socket 的状态改变.

> Recvt is an array with the sockets to test for characters available for reading. Sockets in the sendt array are watched to see if it is OK to immediately write on them.Timeout is the maximum amount of time (in seconds) to wait for a change in status. A nil, negative or omitted timeout value allows the function to block indefinitely.Recvt and sendt can also be empty tables or nil. Non-socket values (or values with non-numeric indices) in the arrays will be silently ignored.

recvt 是一个 socket 的数组, 用来测试读取有效的字符. sendt 也是 socket 的数组, 观察这个集合中的 socket 是否 OK, 立即可写. timeout 是等待一次状态变化的最大时间(以秒为单位). 传 nil 值, 负数或者忽略 timeout 参数, 会让函数无限期的阻塞. recvt 和 sendt 能够是空的 table 或者为 nil. 数组中非 socket 的值(或者非数字索引的值)将被默认忽略.

> The function returns a list with the sockets ready for reading, a list with the sockets ready for writing and an error message. The error message is "timeout" if a timeout condition was met and nil otherwise. The returned tables are doubly keyed both by integers and also by the sockets themselves, to simplify the test if a specific socket has changed status.

这个函数返回一个准备读取数据的 socket 列表, 一个准备写数据的 socket 列表, 一个错误信息. 如果 timeout 的条件触发, 那么错误信息是 “timeout”, 否则返回 nil. 返回的表都是双重双向的, 键控既有整型也有 socket 它们自己(即成对出现的, 整型索引->socket, socket->整型索引), 简化测试如果一个指定的 socket 被改变了状态.

> Note: : select can monitor a limited number of sockets, as defined by the constant socket._SETSIZE. This number may be as high as 1024 or as low as 64 by default, depending on the system. It is usually possible to change this at compile time. Invoking select with a larger number of sockets will raise an error.

注意: select 能够监控有限数量的 socket, 这个数量被作为一个常量定义在 socket._SETSIZE. 这个值默认情况下可能高达 1024 或者低到 64, 这依赖于系统. 它通常可能在编译时就被改变. 使用大于设置的数量的 socket 调用 select 函数会触发一个错误.

> Important note: a known bug in WinSock causes select to fail on non-blocking TCP sockets. The function may return a socket as writable even though the socket isnot ready for sending.

注意事项: 对于 WinSock 的非阻塞 TCP socket, 使用 select 会失败, 这是一个已经的 bug. 虽然某个 socket 没有准备发送, 但是函数可能会将这个 socket 作为可写的返回.

> Another important note: calling select with a server socket in the receive parameter before a call to accept does not guarantee accept will return immediately. Use the settimeout method or accept might block forever.

另外一个注意事项: server socket 按照接受参数的方式调用 select 方法之前调用 accept 方法, 不保证 accept 方法会立即返回. 使用 settimeout 方法或者 accept 可能导致永远的阻塞.

> Yet another note: If you close a socket and pass it to select, it will be ignored.

再一次注意: 如果你关闭了一个 socket, 但是又将它传递给 select, 那么它将被忽略.

> Using select with non-socket objects: Any object that implements getfd and dirty can be used with select, allowing objects from other libraries to be used within a socket.select driven loop.

对 select 使用非 socket 对象: 只要实现了 getfd 和 dirty 方法, 就能用于 select, 允许其他库对象用于 socket.select 的循环驱动.

## socket.sink(mode, socket)

> Creates an LTN12 (luasocket的笔记本内有LTN12的学习说明) sink from a stream socket object.

从一个流 socket 对象创建一个 LTN12 的 sink 对象.

> Mode defines the behavior of the sink. The following options are available:

mode 参数定义了 sink 的行为. 以下的选项是可用的:

> * "http-chunked": sends data through socket after applying the chunked transfer coding, closing the socket when done;
> * "close-when-done": sends all received data through the socket, closing the socket when done;
> * "keep-open": sends all received data through the socket, leaving it open when done.

* "http-chinked": 对传输的数据应用了块传输编码之后通过 socket 将数据发送出去, 发送完成之后关闭 socket;
* "close-when-done": 通过 socket 发送所有接收到的数据, 当发送完成的时候关闭 socket;
* "keep-open": 通过 socket 发送所有接收到的数据, 发送完成后, 保持 socket 是打开状态.

> Socket is the stream socket object used to send the data.

Socket 参数是用于发送数据的 socket 数据流对象.

> he function returns a sink with the appropriate behavior.

这个函数以合适的行为返回一个 sink 对象.

## socket.skip(d [, ret1, ret2 ... retN])

> Drops a number of arguments and returns the remaining.

丢弃掉若干的参数, 并且返回所保留的参数.

> D is the number of arguments to drop. Ret1 to retN are the arguments.

d 参数是丢弃参数的数量. Ret1 到 retN 是参数.

> The function returns retd+1 to retN.

函数返回 retd+1 to retN.

> Note: This function is useful to avoid creation of dummy variables:

注意:这个函数是非常有用的,以避免创建虚拟变量.

```lua
-- get the status code and separator from SMTP server reply
-- 从 SMTP 的回复中得到状态码和分隔符.
local code, sep = socket.skip(2, string.find(line, "^(%d%d%d)(.?)"))
```

## socket.sleep(time)

> Freezes the program execution during a given amount of time.

让当前的执行程序停止运行一段时间.

> Time is the number of seconds to sleep for. If time is negative, the function returns immediately.

time 参数以秒为单位, 设定睡眠的时间. 如果时间是负数, 函数将会立即返回(即不睡眠).

## socket.source(mode, socket [, length])

> Creates an LTN12 source from a stream socket object.

从一个 socket 数据流对象创建出一个 LTN12 的 source 对象.

> Mode defines the behavior of the source. The following options are available:

mode 参数定义 source 的行为. 以下的参数是可选的:

> * "http-chunked": receives data from socket and removes the chunked transfer coding before returning the data;
> * "by-length": receives a fixed number of bytes from the socket. This mode requires the extra argument length;
> * "until-closed": receives data from a socket until the other side closes the connection.

* "http-chunked": 从 socket 接收数据, 并且在返回数据之前移除块传输编码;
* "by-length": 从 socket 接收固定字节长度的数据. 这个模式需要传入 length 参数;
* "until-closed": 从 socket 接收数据, 直到连接的终端断开连接.

> Socket is the stream socket object used to receive the data.

Socket 参数是一个数据流 socket 对象, 用于接收数据.

> The function returns a source with the appropriate behavior.

这个函数用适当的行为返回一个 socket 流数据对象.

## socket._SETSIZE

> The maximum number of sockets that the select function can handle.

select 函数能够处理的 socket 的最大数量.

## socket.try(ret1 [, ret2 ... retN])

> Throws an exception in case of error. The exception can only be caught by the protect function. It does not explode into an error message.

在错误的情况下抛出一个异常, 这个异常只能够被 protect 函数捕获. 它不会发出一个错误的信息.

> Ret1 to retN can be arbitrary arguments, but are usually the return values of a function call nested with try.

Ret1 to retN 能够是任意的参数, 但是通常是函数嵌套调用的返回值.

> The function returns ret1 to retN if ret1 is not nil. Otherwise, it calls error passing ret2.

如果 ret1 不为 nil, 函数返回 ret1 到 retN. 否则将错误传递给 ret2.

```lua
-- connects or throws an exception with the appropriate error message
-- 连接或者抛出一个异常, 并伴随着适当的错误信息.
c = socket.try(socket.connect("localhost", 80))
```

## socket._VERSION

> This constant has a string describing the current LuaSocket version.

这是一个描述当前 LuaSocket 版本的字符串变量.
