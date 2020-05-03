# UDP

- [UDP](#udp)
    - [socket.udp()](#socketudp)
    - [socket.udp6()](#socketudp6)
    - [*:close](#close)
    - [connected:getpeername()](#connectedgetpeername)
    - [*:getsockname()](#getsockname)
    - [*:receive](#receive)
    - [unconnected:receivefrom([size])](#unconnectedreceivefromsize)
    - [*:getoption()](#getoption)
    - [connected:send(datagram)](#connectedsenddatagram)
    - [unconnected:sendto(datagram, ip, port)](#unconnectedsendtodatagram-ip-port)
    - [*:setpeername](#setpeername)
    - [unconnected:setsockname(address, port)](#unconnectedsetsocknameaddress-port)
    - [*:setoption](#setoption)
    - [*:settimeout](#settimeout)

## socket.udp()

> Creates and returns an unconnected IPv4 UDP object. Unconnected objects support the sendto, receive, receivefrom, getoption, getsockname, setoption, settimeout, setpeername, setsockname, and close. The setpeername is used to connect the object.

创建和返回一个未连接的 IPv4 UDP 对象. 未连接的对象支持 sendto, receive, receivefrom, getoption, getsockname, setoption, settimeout, setpeername, setsockname, 和 close. setpeername 函数用于连接的对象.

> In case of success, a new unconnected UDP object returned. In case of error, nil is returned, followed by an error message.

在成功的情况, 返回一个新的未连接的 UDP 对象. 在错误的情况下, 返回 nil, 伴随着错误信息.

## socket.udp6()

> Creates and returns an unconnected IPv6 UDP object. Unconnected objects support the sendto, receive, receivefrom, getoption, getsockname, setoption, settimeout, setpeername, setsockname, and close. The setpeername is used to connect the object.

创建和返回一个未连接的 IPv6 UDP 对象. 未连接的对象支持 sendto, receive, receivefrom, getoption, getsockname, setoption, settimeout, setpeername, setsockname, 和 close. setpeername 函数用于连接的对象.

> In case of success, a new unconnected UDP object returned. In case of error, nil is returned, followed by an error message.

在成功的情况, 返回一个新的未连接的 UDP 对象. 在错误的情况下, 返回 nil, 伴随着错误信息.

> Note: The TCP object returned will have the option "ipv6-v6only" set to true.

注释: 返回的 TCP 对象将有选项 "ipv6-v6only", 并且设置为 true.

## *:close

* connected:close()
* unconnected:close()

> Closes a UDP object. The internal socket used by the object is closed and the local address to which the object was bound is made available to other applications. No further operations (except for further calls to the close method) are allowed on a closed socket.

关闭 UDP 对象. 对象所使用的内部套接字是关闭的，并且该对象被绑定的本地地址可以提供给其他应用程序。在一个关闭的 socket 上没有再进一步的操作(除非再调用 close 方法).

> Note: It is important to close all used sockets once they are not needed, since, in many systems, each socket uses a file descriptor, which are limited system resources. Garbage-collected objects are automatically closed before destruction, though.

注释: 一次性关闭它们所有不再需要的 socket 是非常重要的, 因为, 在很多操作系统中, 每个 socket 使用一个文件描述符, 它们对于系统来说是有限的资源. 不过在垃圾回收销毁对象以前会自动关闭.

## connected:getpeername()

> Retrieves information about the peer associated with a connected UDP object.

检索与一个连接的 UDP 对象有关联的远程终端信息.

>Returns a string with the IP address of the peer, the port number that peer is using for the connection, and a string with the family ("inet" or "inet6"). In case of error, the method returns nil.

返回一个远程终端的 IP 地址(字符串类型), 远程终端的连接端口(数字类型), 使用的协议族("inet" 或 "inet6")(字符串类型). 在错误的情况, 方法返回 nil.

> Note: It makes no sense to call this method on unconnected objects.

注释: 对于一个没有连接的对象调用这个方法是没有意义的.

## *:getsockname()

* connected:getsockname()
* unconnected:getsockname()

> Returns the local address information associated to the object.

返回关联对象的本地信息.

> The method returns a string with local IP address, a number with the local port, and a string with the family ("inet" or "inet6"). In case of error, the method returns nil.

该方法返回一个 IP 地址的字符串, 本地占用端口的数字, 使用协议族的字符串("inet" 或 "inet6"). 在错误的情况下, 方法返回 nil.

> Note: UDP sockets are not bound to any address until the setsockname or the sendto method is called for the first time (in which case it is bound to an ephemeral port and the wild-card address).

注释: 直到 setsockname 或 sendto 方法第一次被调用(在这种情况下, 它被绑定到一个临时的端口和通用地址)前, UDP 对象不会绑定到任何地址.

## *:receive

* connected:receive([size])
* unconnected:receive([size])

> Receives a datagram from the UDP object. If the UDP object is connected, only datagrams coming from the peer are accepted. Otherwise, the returned datagram can come from any host.

从 UDP 对象收集到一个数据报文. 如果是连接的 UDP 对象, 数据报文只会从连接的远程终端接收到. 否则, 数据报文会从任何一台主机发来.

> The optional size parameter specifies the maximum size of the datagram to be retrieved. If there are more than size bytes available in the datagram, the excess bytes are discarded. If there are less then size bytes available in the current datagram, the available bytes are returned. If size is omitted, the maximum datagram size is used (which is currently limited by the implementation to 8192 bytes).

可选 size 参数指定检索数据报文的最大大小. 如果有超过 size 大小的数据报文, 超过的字节将会被丢弃. 如果有效的字节数小于 size, 有效的字节数将被返回. 如果忽略 size 参数, 将使用最大的数据报文大小(当前实现的最大大小是 8192 字节).

> In case of success, the method returns the received datagram. In case of timeout, the method returns nil followed by the string 'timeout'.

在成功的情况下, 方法返回接收的数据包. 在超时的情况下, 返回 nil, 并且伴随着 'timeout' 字符串.

## unconnected:receivefrom([size])

> Works exactly as the receive method, except it returns the IP address and port as extra return values (and is therefore slightly less efficient).

它的工作方式和 receive 方法一样, 除外还会返回 IP 地址和端口号(因此效率比较低).

## *:getoption()

* connected:getoption()
* unconnected:getoption()

> Gets an option value from the UDP object. See setoption for description of the option names and values.

获得一个 UDP 对象选项的值. 查看 setoption 函数, 它对选项的名字和值做了描述.

> Option is a string with the option name.

选项的名字是一个字符串类型.

> * 'dontroute'
> * 'broadcast'
> * 'reuseaddr'
> * 'reuseport'
> * 'ip-multicast-loop'
> * 'ipv6-v6only'
> * 'ip-multicast-if'
> * 'ip-multicast-ttl'
> * 'ip-add-membership'
> * 'ip-drop-membership'

> The method returns the option value in case of success, or nil followed by an error message otherwise.

在成功的情况下返回选项的值, 否则返回 nil, 并且伴随着错误信息.

## connected:send(datagram)

> Sends a datagram to the UDP peer of a connected object.

发送一个数据包给远程连接的终端.

>Datagram is a string with the datagram contents. The maximum datagram size for UDP is 64K minus IP layer overhead. However datagrams larger than the link layer packet size will be fragmented, which may deteriorate performance and/or reliability.

datagram 参数是一个字符串类型, 表示数据报的内容.  UDP 数据报的最大大小是 64K 减去 IP 协议层的数据报头. 然而数据报文大于链路层的数据包尺寸, 所以将会被切片, 这可能使性能和/或可靠性恶化.

> If successful, the method returns 1. In case of error, the method returns nil followed by an error message.

如果成功, 方法将返回 1. 如果失败, 方法返回 nil, 并且伴随错误信息.

> Note: In UDP, the send method never blocks and the only way it can fail is if the underlying transport layer refuses to send a message to the specified address (i.e. no interface accepts the address).

注释: 对于 UDP, send 方法从不阻塞, 但是失败的唯一方法就是底层传输层拒绝将消息发送到指定的地址(例如: 没有接口接受地址).

## unconnected:sendto(datagram, ip, port)

> Sends a datagram to the specified IP address and port number.

发送数据报文到指定的 IP 地址和端口.

> Datagram is a string with the datagram contents. The maximum datagram size for UDP is 64K minus IP layer overhead. However datagrams larger than the link layer packet size will be fragmented, which may deteriorate performance and/or reliability. Ip is the IP address of the recipient. Host names are not allowed for performance reasons. Port is the port number at the recipient.

datagram 参数是一个字符串类型, 包含数据报文的内容. UDP 数据报的最大大小是 64K 减去 IP 协议层的数据报头. 然而数据报文大于链路层的数据包尺寸, 所以将会被切片, 这可能使性能和/或可靠性恶化. ip 参数指的是接受者的 ip 地址, 字符串类型. 因为性能的原因, 不能使用主机名. port 参数表示接收者的端口, 数字类型.

> If successful, the method returns 1. In case of error, the method returns nil followed by an error message.

如果成功, 函数返回 1. 在失败的情况下, 函数返回 nil 并且伴随着错误信息.

> Note: In UDP, the send method never blocks and the only way it can fail is if the underlying transport layer refuses to send a message to the specified address (i.e. no interface accepts the address).

注释: 对于 UDP, send 方法从不阻塞, 但是失败的唯一方法就是底层传输层拒绝将消息发送到指定的地址(例如: 没有接口接受地址).

## *:setpeername

* connected:setpeername('*')
* unconnected:setpeername(address, port)

> Changes the peer of a UDP object. This method turns an unconnected UDP object into a connected UDP object or vice versa.

改变一个对端的 UDP 对象. 这个方法将一个未连接的 UDP 对象转换成一个连接的 UDP 对象, 反之亦然.

> For connected objects, outgoing datagrams will be sent to the specified peer, and datagrams received from other peers will be discarded by the OS. Connected UDP objects must use the send and receive methods instead of sendto and receivefrom.

对于连接的对象, 发出的数据报文将被发送到指定的对端, 并且从其他对端接收到的数据报文将被操作系统丢弃掉. 连接的 UDP 对象必须使用 send 和 receive 方法, 用来代替 sendto 和 receivefrom 方法.

> Address can be an IP address or a host name. Port is the port number. If address is '*' and the object is connected, the peer association is removed and the object becomes an unconnected object again. In that case, the port argument is ignored.

address 可以是一个 ip 地址或者主机名. port 参数是表示端口号. 如果 address 参数是 *, 并且对象是连接的, 那么连接的对端将被移除, 并且再次变为一个未连接的对象. 在这种情况下, port 参数将被忽略.

> In case of error the method returns nil followed by an error message. In case of success, the method returns 1.

在错误的情况下, 函数返回 nil, 并且伴随着错误信息. 在成功的情况下, 返回 1.

> Note: Since the address of the peer does not have to be passed to and from the OS, the use of connected UDP objects is recommended when the same peer is used for several transmissions and can result in up to 30% performance gains.

注释: 因为对端的地址不必从操作系统传递,  所以推荐使用连接的 UDP 对象, 当需要给同一个对端传输数据时, 使用连接的 UDP 对象可以获得 30% 的性能提升.

> Note: Starting with LuaSocket 3.0, the host name resolution depends on whether the socket was created by socket.udp or socket.udp6. Addresses from the appropriate family are tried in succession until the first success or until the last failure.

注释: 从 LuaSocket 3.0 开始, 主机名的解析取决于 socket 对象是否被 socket.udp 或 socket.udp6 创建. 地址来自于对应的协议族通过不断的尝试, 该尝试直到第一次成功或者最后一次失败才停止.

## unconnected:setsockname(address, port)

> Binds the UDP object to a local address.

在本地绑定一个 UDP 对象.

> Address can be an IP address or a host name. If address is '*' the system binds to all local interfaces using the constant INADDR_ANY. If port is 0, the system chooses an ephemeral port.

address 参数可以是一个 IP 地址或者主机名. 如果 address 是 ‘*’, 系统会使用 INADDR_ANY 常量绑定所有的本地通信接口. 如果端口为 0, 系统会选择一个临时的端口.

> If successful, the method returns 1. In case of error, the method returns nil followed by an error message.

如果成功, 方法返回 1. 在错误的情况下, 方法返回 nil 并且伴随着错误信息.

> Note: This method can only be called before any datagram is sent through the UDP object, and only once. Otherwise, the system automatically binds the object to all local interfaces and chooses an ephemeral port as soon as the first datagram is sent. After the local address is set, either automatically by the system or explicitly by setsockname, it cannot be changed.

注释: 这个方法只能在数据报文被 UDP 对象发送之前被调用, 并且只需要调用一次. 否则, 系统自动的绑定对象到所有本地通信接口, 同时选择一个临时端口, 尽可能快的将数据报文发送出去. 既可以通过操作系统自动的设置地址, 也可以通过 setsockname 方法手动设置地址, 但是在本地地址设置之后就不能再改变.

## *:setoption

* connected:setoption(option [, value])
* unconnected:setoption(option [, value])

> Sets options for the UDP object. Options are only needed by low-level or time-critical applications. You should only modify an option if you are sure you need it.

设置 UDP 对象的选项值. 选项只在需要操作底层或者对时序要求严格的应用程序中才会被设置. 你应该只修改你需要修改的选项.

> Option is a string with the option name, and value depends on the option being set:

option 参数是一个字符串类型, 表示选项的名字, 值取决于被设置的选项:

> * 'dontroute': Indicates that outgoing messages should bypass the standard routing facilities. Receives a boolean value;
> * 'broadcast': Requests permission to send broadcast datagrams on the socket. Receives a boolean value;
> * 'reuseaddr': Indicates that the rules used in validating addresses supplied in a bind() call should allow reuse of local addresses. Receives a boolean value;
> * 'reuseport': Allows completely duplicate bindings by multiple processes if they all set 'reuseport' before binding the port. Receives a boolean value;
> * 'ip-multicast-loop': Specifies whether or not a copy of an outgoing multicast datagram is delivered to the sending host as long as it is a member of the multicast group. Receives a boolean value;
> * 'ipv6-v6only': Specifies whether to restrict inet6 sockets to sending and receiving only IPv6 packets. Receive a boolean value;
> * 'ip-multicast-if': Sets the interface over which outgoing multicast datagrams are sent. Receives an IP address;
> * 'ip-multicast-ttl': Sets the Time To Live in the IP header for outgoing multicast datagrams. Receives a number;
> * 'ip-add-membership': Joins the multicast group specified. Receives a table with fields multiaddr and interface, each containing an IP address;
> * 'ip-drop-membership': Leaves the multicast group specified. Receives a table with fields multiaddr and interface, each containing an IP address.

* ‘dontroute’: 指明发送的消息应该绕过标准的路由设施, 接收一个布尔值;
* ‘broadcast’: 在一个 socket 上请求发送广播数据报文的权限, 接收一个布尔值;
* ‘reuseaddr’: 指明 bind() 方法是否返回有效的可复用的本地地址, 接收一个布尔值;
* ‘reuseport’: 允许多个进程能够完整的被重复绑定, 但是这些进程必须在绑定前设置了 ‘reuseport’, 接收一个布尔值;
* ‘ip-multicast-loop’: 指明是否将组播数据报文副本递交给发送的主机, 只要当前是组播组的成员, 接收一个布尔值.
* ‘ipv6-v6only’: 指明是否限定 inet6 的socket 只能发送和接收 IPv6 的数据包, 接收一个布尔值;
* ‘ip-multicast-if’: 设置发送组播数据报文的网络接口, 接收一个 IP 地址;
* ‘ip-multicast-ttl’: 在 IP 的消息头设置组播报文的生存时间, 接收一个数字;
* ‘ip-addr-membership’: 指明加入的组播组. 接收一个 table 类型, 它包含两个字段, multiaddr 和 interface, 表示的都是 IP 地址(multiaddr 表示加入的广播组 IP 地址, interface 表示本机需要假如广播组的网络接口 IP 地址);
* ‘ip-drop-membership’: 指明退出的组播组. 接收一个 table 类型, 它包含两个字段, multiaddr 和 interface, 表示的都是 IP 地址;

> The method returns 1 in case of success, or nil followed by an error message otherwise.

这个方法成功时返回 1, 否则返回 nil, 并且伴随错误信息.

> Note: The descriptions above come from the man pages.

注释: 以上的描述都来自操作说明.

## *:settimeout

* connected:settimeout(value)
* unconnected:settimeout(value)

> Changes the timeout values for the object. By default, the receive and receivefrom operations are blocking. That is, any call to the methods will block indefinitely, until data arrives. The settimeout function defines a limit on the amount of time the functions can block. When a timeout is set and the specified amount of time has elapsed, the affected methods give up and fail with an error code.

改变对象的超时时间设置. 默认情况下, receive 和 receivefrom 操作是阻塞的. 换言之, 任何的方法调用都是立即阻塞的, 直到数据到达. 设置 settimeout 函数定义了阻塞的总时间. 当设置了超时时间, 并且经过了超时时间时, 收到影响的方法将放弃处理, 并且返回一个错误代码.

> The amount of time to wait is specified as the value parameter, in seconds. The nil timeout value allows operations to block indefinitely. Negative timeout values have the same effect.

value 参数设置的是等待的时间, 以秒为单位. 如果传入的值是 nil 或者负数, 那么允许立即阻塞.

> Note: In UDP, the send and sendto methods never block (the datagram is just passed to the OS and the call returns immediately). Therefore, the settimeout method has no effect on them.

注意: 在 UDP 中, send 和 sendto 函数从不阻塞(数据报文只是传递给操作系统, 同时立即返回). 因此, settimeout 方法对这两个函数无效.

> Note: The old timeout method is deprecated. The name has been changed for sake of uniformity, since all other method names already contained verbs making their imperative nature obvious.

注意: 老的 timeout 方法弃用了. 为了统一名称而更改, 因此所有其他方法的名称已经包含动词使他们的特性明显。
