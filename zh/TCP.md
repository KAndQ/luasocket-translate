# TCP (in socket) API

## socket.tcp()

> Creates and returns an IPv4 TCP master object. A master object can be transformed into a server object with the method listen (after a call to bind) or into a client object with the method connect. The only other method supported by a master object is the close method.

创建和返回一个 IPv4 的 TCP mater 对象. 一个 master 对象能够转化为一个 server 对象, 可以使用 listen 和 bind 方法; 也可以是一个客户端使用的对象, 使用 connect 方法. 剩下 master 对象支持的唯一方法就是 close 方法.

> In case of success, a new master object is returned. In case of error, nil is returned, followed by an error message.

调用成功, 返回一个新的 master 对象. 调用失败, 返回 nil 值, 并且同时携带一个错误信息.

## socket.tcp6()

> Creates and returns an IPv6 TCP master object. A master object can be transformed into a server object with the method listen (after a call to bind) or into a client object with the method connect. The only other method supported by a master object is the close method.

创建和返回一个 IPv6 的 TCP mater 对象. 一个 master 对象能够转化为一个 server 对象, 可以使用 listen 和 bind 方法; 也可以是一个客户端使用的对象, 使用 connect 方法. 剩下 master 对象支持的唯一方法就是 close 方法.

> In case of success, a new master object is returned. In case of error, nil is returned, followed by an error message.

在成功的情况下, 一个新的 master 对象会被返回. 在失败的情况下, 返回 nil 值, 伴随着错误信息.

> Note: The TCP object returned will have the option "ipv6-v6only" set to true.

注意: 返回的 TCP 对象将有一个选项 ”ipv6-v6only” 设置为 true.

## server:accept()

> Waits for a remote connection on the server object and returns a client object representing that connection.

等待一个远程终端连接到 server 对象, 并且返回一个表示该连接的 client 对象.

> If a connection is successfully initiated, a client object is returned. If a timeout condition is met, the method returns nil followed by the error string 'timeout'. Other errors are reported by nil followed by a message describing the error.

如果一个连接成功初始化, 一个 client 对象将会返回. 如果一个 timeout 情况出现, 这个方法返回 nil 和错误信息 ‘timeout’. 其他的错误信息也是同样的方式返回.

> Note: calling socket.select with a server object in the recvt parameter before a call to accept does not guarantee accept will return immediately. Use thesettimeout method or accept might block until another client shows up.

注意: server socket 按照接受参数的方式调用 select 方法之前调用 accept 方法, 不保证 accept 方法会立即返回. 使用 settimeout 方法或者 accept 可能导致永远的阻塞, 直到另外一个客户端连接.

## master:bind(address, port)

> Binds a master object to address and port on the local host.

在本机上绑定一个 master 对象到指定的地址和端口.

> Address can be an IP address or a host name. Port must be an integer number in the range [0..64K). If address is '*', the system binds to all local interfaces using theINADDR_ANY constant or IN6ADDR_ANY_INIT, according to the family. If port is 0, the system automatically chooses an ephemeral port.

address 可以是一个 IP 地址, 也可以是一个主机名. port 必须是一个整型数字, 范围在 [0, 65535). 如果 address 是 ‘*’, 系统根据协议族使用 INADDR_ANY 或 IN6ADDR_ANY_INIT 常量绑定所有的本地接口. 如果 port 为 0, 系统自动选择一个临时的端口.

> In case of success, the method returns 1. In case of error, the method returns nil followed by an error message.

在成功的情况下, 这个方法返回 1. 在错误的情况下, 这个方法返回 nil, 并伴随着错误信息.

> Note: The function socket.bind is available and is a shortcut for the creation of server sockets.

注意: socket.bind 这个函数是一个创建 server socket 的捷径.

## *:close()

* master:close()
* client:close()
* server:close()

> Closes a TCP object. The internal socket used by the object is closed and the local address to which the object was bound is made available to other applications. No further operations (except for further calls to the close method) are allowed on a closed socket.

关闭一个 TCP 对象. 内部的 socket 通过这个对象被关闭, 并且之前被绑定的本地地址可以用于其他的应用程序. 对关闭的 socket 不会再允许进一步的操作(除非再调用 close 方法).

> Note: It is important to close all used sockets once they are not needed, since, in many systems, each socket uses a file descriptor, which are limited system resources. Garbage-collected objects are automatically closed before destruction, though.

注意: 关闭不需要的 socket 是非常重要的, 因为在很多的系统中, 每个 socket 使用 1 个文件描述符, 这是有限的系统资源. 不过在 lua 垃圾回收销毁对象的时候会自动关闭.

## master:connect(address, port)

> Attempts to connect a master object to a remote host, transforming it into a client object. Client objects support methods send, receive, getsockname, getpeername,settimeout, and close.

试图连接一个在远端的 master 对象, 将自己转化为 client 对象. client 对象支持 send, receive, getsockname, getpeername, settimeout 和 close 方法.

> Address can be an IP address or a host name. Port must be an integer number in the range [1..64K).

address 可以是一个 IP 地址或者主机名. port 必须是一个在 [1, 65535) 范围内的整数.

> In case of error, the method returns nil followed by a string describing the error. In case of success, the method returns 1.

在错误的情况下, 这个方法返回 nil 和错误信息. 在成功的情况下, 这个方法返回 1.

> Note: The function socket.connect is available and is a shortcut for the creation of client sockets.

注意: socket.connect 函数是一个创建 client socket 的快捷方式.

> Note: Starting with LuaSocket 2.0, the settimeout method affects the behavior of connect, causing it to return with an error in case of a timeout. If that happens, you can still call socket.select with the socket in the sendt table. The socket will be writable when the connection is established.

注意: 自 LuaSocket 2.0 开始, settimeout 方法影响 connect 方法, 在 timeout 的情况下会返回一个错误. 如果 timeout 情况发生了, 你仍然能够调用 socket.select 方法, 得到 sendt 里面的 socket. 这些都是连接建立之后可写的 socket.

> Note: Starting with LuaSocket 3.0, the host name resolution depends on whether the socket was created by socket.tcp or socket.tcp6. Addresses from the appropriate family are tried in succession until the first success or until the last failure.

注意: 从 LuaSocket 3.0 开始, 主机名字的解析取决于 socket 对象是否被 socket.tcp 或者 socket.tcp6 创建. 地址来自于对应的协议族通过不断的尝试, 该尝试直到第一次成功或者最后一次失败才停止.

## client:getpeername()

> Returns information about the remote side of a connected client object.

返回远程连接端的 client 对象信息.

> Returns a string with the IP address of the peer, the port number that peer is using for the connection, and a string with the family ("inet" or "inet6"). In case of error, the method returns nil.

返回远程终端的IP地址, 字符串形式, 远程终端使用的端口号, 使用的协议族(“inet” 或者 “inet6”). 在错误的情况下, 这个方法返回 nil.

> Note: It makes no sense to call this method on server objects.

注意: 在 server 对象上调用这个方法是没有意义的.

## *:getsockname()

* master:getsockname()
* client:getsockname()
* server:getsockname()

> Returns the local address information associated to the object.

返回与当前对象相关联的本地地址信息.

> The method returns a string with local IP address, a number with the local port, and a string with the family ("inet" or "inet6"). In case of error, the method returnsnil.

这个方法返回本地地址的字符串, 本地端口号, 协议族(“inet” 或者 “inet6”). 在错误的情况下, 该方法返回 nil.

## *:getstats()

* master:getstats()
* client:getstats()
* server:getstats()

> Returns accounting information on the socket, useful for throttling of bandwidth.

返回 socket 的统计信息, 用于带宽的限制.

> The method returns the number of bytes received, the number of bytes sent, and the age of the socket object in seconds.

这个方法返回接收到的总字节数量, 发送的字节数量, socket 的存活时间(以秒为单位).

## master:listen(backlog)

> Specifies the socket is willing to receive connections, transforming the object into a server object. Server objects support the accept, getsockname, setoption,settimeout, and close methods.

指定 socket 资源接受连接, 自身将转化为 server 对象. server 对象支持, accept, getsockname, set option, settimeout 和 close 方法.

> The parameter backlog specifies the number of client connections that can be queued waiting for service. If the queue is full and another client attempts connection, the connection is refused.

参数 backlog 客户端等待连接队列的最大数量. 如果队列已满, 企图连接的客户端将被拒绝.

> In case of success, the method returns 1. In case of error, the method returns nil followed by an error message.

在成功的情况下返回 1. 在失败的情况下返回 nil 和一个错误信息.

## client:receive([pattern [, prefix]])

> Reads data from a client object, according to the specified read pattern. Patterns follow the Lua file I/O format, and the difference in performance between all patterns is negligible.

通过 client 对象读取数据, 通过指定的读取模式. 模式遵从 Lua 文件的 I/O 格式, 性能在所有模式之间的差异可以忽略不计.

> Pattern can be any of the following:

可以是下面任何的模式:

> * '\*a': reads from the socket until the connection is closed. No end-of-line translation is performed;
> * '\*l': reads a line of text from the socket. The line is terminated by a LF character (ASCII 10), optionally preceded by a CR character (ASCII 13). The CR and LF characters are not included in the returned line. In fact, all CR characters are ignored by the pattern. This is the default pattern;
> * number: causes the method to read a specified number of bytes from the socket.

* ‘*a’: 从 socket 读取数据, 直到 socket 连接被关闭. 没有执行”行结束符”的转换;
* ‘*l’: 从 socket 读取一行数据. 这一行会被 LF(ASCII 10) 字符截断, CR(ASCII 13)字符的优先级更高. LF 和 CR 字符将不会包含在返回的字符串中. 事实上, 所有的 CR 字符会被模式忽略. 这是一个默认模式;
* number: 指定从 socket 读取 number 个字节的数据.

> Prefix is an optional string to be concatenated to the beginning of any received data before return.

Prefix 参数是一个可选的参数, 他会被添加到任何的接收数据之前.

> If successful, the method returns the received pattern. In case of error, the method returns nil followed by an error message, followed by a (possibly empty) string containing the partial that was received. The error message can be the string 'closed' in case the connection was closed before the transmission was completed or the string 'timeout' in case there was a timeout during the operation.

如果成功, 这个方法返回接收到的模式. 如果错误, 方法返回 nil, 错误信息, 和部分接收到的字符串(该字符串可能为空). 如果在传输完成之前关闭连接或者有一个字符串的操作超时, 那么会得不到错误信息.

> Important note: This function was changed severely. It used to support multiple patterns (but I have never seen this feature used) and now it doesn't anymore. Partial results used to be returned in the same way as successful results. This last feature violated the idea that all functions should return nil on error. Thus it was changed too.

重要注释: 这个函数被狠狠的修改了!(做了很大调整). 它曾经支持多种模式(但是我从没见过这些特性被使用), 但是现在不再这样了. 接收的片段数据结果会和成功结果一样返回, 这最近的特征违反了所有函数在错误的情况下返回 nil. 因此它也被修改了.

## client:send(data [, i [, j]])

> Sends data through client object.

通过 client 对象发送数据.

> Data is the string to be sent. The optional arguments i and j work exactly like the standard string.sub Lua function to allow the selection of a substring to be sent.

被发送出去的数据是字符串. 可选参数 i 和 j 的工作原理如同标准的 string.sub lua 函数, 截取字符串的一个子串发送.

> If successful, the method returns the index of the last byte within [i, j] that has been sent. Notice that, if i is 1 or absent, this is effectively the total number of bytes sent. In case of error, the method returns nil, followed by an error message, followed by the index of the last byte within [i, j] that has been sent. You might want to try again from the byte following that. The error message can be 'closed' in case the connection was closed before the transmission was completed or the string 'timeout' in case there was a timeout during the operation.

如果发送成功, 方法返回在[i, j]内还未发送字节的起始索引. 请注意, 如果 i 是 1 或者被忽略, 所有的数据将被发送. 在失败的情况下, 该方法返回 nil, 并且携带错误信息, 同时也携带着在[i, j]内还未发送字节的索引. 你可能希望从返回的字符串索引处再发送一次数据. 如果在传输完成之前关闭连接或者有一个字符串的操作超时, 那么会得不到错误信息.

> Note: Output is not buffered. For small strings, it is always better to concatenate them in Lua (with the '..' operator) and send the result in one call instead of calling the method several times.

注意: 输出没有被缓存. 对于一些小字符串, 在 lua 中较好的方式是使用(.. 操作符)来连接它们, send 是一次调用的结果, 而不是多次调用的结果.

## *:setoption(option [, value])

* client:setoption(option [, value])
* server:setoption(option [, value])

> Sets options for the TCP object. Options are only needed by low-level or time-critical applications. You should only modify an option if you are sure you need it.

设置选项使用于 TCP 对象. 设置选项一般只用于底层或者时序要求严格的应用程序. 你应该只修改你需要修改的选项.

> Option is a string with the option name, and value depends on the option being set:

option 参数是选项的字符串, value 参数依赖于设置的 option 参数:

> * 'keepalive': Setting this option to true enables the periodic transmission of messages on a connected socket. Should the connected party fail to respond to these messages, the connection is considered broken and processes using the socket are notified;
> * 'linger': Controls the action taken when unsent data are queued on a socket and a close is performed. The value is a table with a boolean entry 'on' and a numeric entry for the time interval 'timeout' in seconds. If the 'on' field is set to true, the system will block the process on the close attempt until it is able to transmit the data or until 'timeout' has passed. If 'on' is false and a close is issued, the system will process the close in a manner that allows the process to continue as quickly as possible. I do not advise you to set this to anything other than zero;
> * 'reuseaddr': Setting this option indicates that the rules used in validating addresses supplied in a call to bind should allow reuse of local addresses;
> * 'tcp-nodelay': Setting this option to true disables the Nagle's algorithm for the connection;
> * 'ipv6-v6only': Setting this option to true restricts an inet6 socket to sending and receiving only IPv6 packets.

* ‘keepalive’: 设置这个选项为 true, 使一个 socket 定期的发送消息. 如果连接方无法对这些消息做出响应, 那么认为该连接坏掉了, 通知使用的 socket 做处理.
* ‘linger’: 控制着当 socket 发生 close 操作, 并且 socket 中有为发送的数据队列时的行为. 这个值是一个 table 类型, ‘on’ 字段是布尔类型, ‘timeout’ 字段是数字类型(以秒为单位). 如果 ‘on’ 字段设置为 true, 当试图关闭 socket 的时候, 系统将阻塞当前处理, 直到数据传送完成, 或者到达 ‘timeout’ 的设置时间引发超时. 如果 ‘on’ 为 false同时 close 操作发生, 系统在某种程度上将以尽可能最快的方式处理. 我不建议你设置这个选项, 除了 0.
* ‘reuseaddr’: 设置这个选项表明提供可用地址的规则, 在调用 bind 方法的时候, 允许重用本地地址;
* ‘tcp-nodelay’: 设置这个选项为 true, 该连接禁用 Nagle’s 算法.
* ‘ipv6-v6only’: 设置这个选项为 true, 限制一个 inrt6 的 socket 只发送和接收 IPv6 的数据包.

> The method returns 1 in case of success, or nil followed by an error message otherwise.

这个函数成功时返回 1, 否则返回 nil, 伴随着错误信息.

> Note: The descriptions above come from the man pages.

注释: 以上的描述都是来自操作说明.

## *:getoption(option)

* client:getoption(option)
* server:getoption(option)

> Gets options for the TCP object. See setoption for description of the option names and values.

获得 TCP 对象的选项. 查看 setoption 的选项的名字和值.

> Option is a string with the option name.

选项的名字是一个 string 类型.

* 'keepalive'
* 'linger'
* 'reuseaddr'
* 'tcp-nodelay'

> The method returns the option value in case of success, or nil followed by an error message otherwise.

成功时返回选项的值, 否则返回 nil 和错误信息.

## *:setstats(received, sent, age)

* master;
* client;
* server;

> Resets accounting information on the socket, useful for throttling of bandwidth.

重置 socket 的统计信息, 用于带宽的调节.

> Received is a number with the new number of bytes received. Sent is a number with the new number of bytes sent. Age is the new age in seconds.

receive 表示接收到的字节数. sent 发送的字节数. age 新的使用时间(以秒为单位).

> The method returns 1 in case of success and nil otherwise.

如果设置成功返回1, 否则返回 nil.

## *:settimeout(value [, mode])

* client:settimeout(value [, mode])
* server:settimeout(value [, mode])

> Changes the timeout values for the object. By default, all I/O operations are blocking. That is, any call to the methods send, receive, and accept will block indefinitely, until the operation completes. The settimeout method defines a limit on the amount of time the I/O methods can block. When a timeout is set and the specified amount of time has elapsed, the affected methods give up and fail with an error code.

改变对象的 timeout 值. 默认情况下, 所有的 I/O 操作都是阻塞的. 换言之, 任何方式调用 send, receive, accept 方法将会立即阻塞, 直到操作完成. 这个 settimeout 方法定义了一个 I/O 方法阻塞操作的极限时间. 当设置一个 timeout 值, 并且经过了指定的时间, 受到影响的方法将放弃阻塞并且用一个错误码表示失败.

> The amount of time to wait is specified as the value parameter, in seconds. There are two timeout modes and both can be used together for fine tuning:

value 参数是设定的等待时间, 以秒为单位. 有两种 timeout 模式, 两种都可用于做微调:

> * 'b': block timeout. Specifies the upper limit on the amount of time LuaSocket can be blocked by the operating system while waiting for completion of any single I/O operation. This is the default mode;
> * 't': total timeout. Specifies the upper limit on the amount of time LuaSocket can block a Lua script before returning from a call.

* ‘b’: 阻塞时间. 当等待任何一个 I/O 操作完成时, 指定 LuaSocket 被操作系统阻塞的等待上限时间. 这是默认模式.
* ’t’: 总时间. 指定 LuaSocket 能够阻塞 lua 脚本从一个调用返回的总时间.

> The nil timeout value allows operations to block indefinitely. Negative timeout values have the same effect.

nil timeout 值将无限期的阻塞. 负数 timeout 值也是同样的效果.

> Note: although timeout values have millisecond precision in LuaSocket, large blocks can cause I/O functions not to respect timeout values due to the time the library takes to transfer blocks to and from the OS and to and from the Lua interpreter. Also, function that accept host names and perform automatic name resolution might be blocked by the resolver for longer than the specified timeout value.

注意: 虽然 timeout 值在 LuaSocket 中拥有毫秒的精度, 由于时间库从操作系统和 lua 解释器之间传递阻塞, 所以大多数的阻塞使 I/O 函数不遵守 timeout 的值. 另外, 接受主机名字的函数通过解析器自动的执行名字解析, 这个阻塞操作可能会久于指定的 timeout 值.

> Note: The old timeout method is deprecated. The name has been changed for sake of uniformity, since all other method names already contained verbs making their imperative nature obvious.

注意: 旧的 timeout 方法已经被弃用了. 为了统一名称名字已经更改, 因此所有其他方法名称已经明显的包含动词, 这使他们特性明显.

## client:shutdown(mode)

> Shuts down part of a full-duplex connection.

关闭全双工连接的一端.

> Mode tells which way of the connection should be shut down and can take the value:

mode 参数表示应该关闭连接的方式, 以下是可传递的值:

> * "both": disallow further sends and receives on the object. This is the default mode;
> * "send": disallow further sends on the object;
> * "receive": disallow further receives on the object.

* “both”: 对象不再进一步的发送和接收, 这是默认的模式;
* “send”: 对象不再进一步发送;
* “receive”: 对象不再进一步接收;

> This function returns 1.

这个函数返回 1.

## *:dirty()

* master:dirty()
* client:dirty()
* server:dirty()

> Check the read buffer status.

检查当前可读内存的状态.

> Returns true if there is any data in the read buffer, false otherwise.

如果有任何数据可读则返回 true, 否则返回 false.

> Note: This is an internal method, any use is unlikely to be portable.

注意: 这是一个内部方法, 任何直接的使用都不太可能.

## *:getfd()

* master:getfd()
* client:getfd()
* server:getfd()

> Returns the underling socket descriptor or handle associated to the object.

返回底层的 socket 描述符或者处理相关对象的句柄(传给 socket.select 的参数必须实现这个方法).

> The descriptor or handle. In case the object has been closed, the return will be -1.

描述符或者句柄, 在对象被关闭的情况下, 返回 -1.

> Note: This is an internal method, any use is unlikely to be portable.

注意: 这是一个内部方法, 任何直接的使用都不太可能.

## *:setfd(fd)

* master:setfd(fd)
* client:setfd(fd)
* server:setfd(fd)

> Sets the underling socket descriptor or handle associated to the object. The current one is simply replaced, not closed, and no other change to the object state is made.

设置底层的 socket 描述符或者关联对象的句柄. 当前功能只是简单的替换, 并不关闭, 而且也改变对象的其他状态.

> No return value.

无返回值.

> Note: This is an internal method, any use is unlikely to be portable.

注意: 这是一个内部方法, 任何直接的使用都不太可能.
