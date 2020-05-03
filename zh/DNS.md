# DNS

- [DNS](#dns)
    - [socket.dns.getaddrinfo(address)](#socketdnsgetaddrinfoaddress)
    - [socket.dns.gethostname()](#socketdnsgethostname)
    - [socket.dns.tohostname(address)](#socketdnstohostnameaddress)
    - [socket.dns.toip(address)](#socketdnstoipaddress)

> IPv4 name resolution functions dns.toip and dns.tohostname return all information obtained from the resolver in a table of the form:

IPv4 名字解析函数 dns.toip 和 dns.tohostname 通过解析器返回所有获得的信息, 信息存放在下表中:

```lua
resolved4 = {
  name = canonic-name,
  alias = alias-list,
  ip = ip-address-list
}
```

> Note that the alias list can be empty.

注意 alias 字段的值是一个列表, 它可能为空.

> The more general name resolution function dns.getaddrinfo, which supports both IPv6 and IPv4, returns all information obtained from the resolver in a table of the form:

更通用的名字解析函数是 dns.getaddrinfo, 它即支持 IPv6 和 IPv4, 通过解析器得到所有的信息, 表结构如下所示:

```lua
resolved6 = {
  [1] = {
    family = family-name-1,
    addr = address-1
  },
  ...
  [n] = {
    family = family-name-n,
    addr = address-n
  }
}
```

> Here, family contains the string "inet" for IPv4 addresses, and "inet6" for IPv6 addresses.

这里, 协议族包含字符串 "inet" 表示 IPv4 地址, "inet6" 表示 IPv6 地址.

## socket.dns.getaddrinfo(address)

> Converts from host name to address.

将主机名转化为地址.

> Address can be an IPv4 or IPv6 address or host name.

address 参数能够是 IPv4 或者 IPv6 地址或者主机名.

> The function returns a table with all information returned by the resolver. In case of error, the function returns nil followed by an error message.

这个函数返回一个表, 表里面包含了从解析器返回的所有信息. 在出错的情况下, 这个函数返回 nil, 并且伴随着一个错误信息.

## socket.dns.gethostname()

> Returns the standard host name for the machine as a string.

以字符串的形式反馈机器的主机名.

## socket.dns.tohostname(address)

> Converts from IPv4 address to host name.

将一个 IPv4 地址转化为主机名.

> Address can be an IP address or host name.

address 参数能够是 IP 地址, 也可以是主机名.

> The function returns a string with the canonic host name of the given address, followed by a table with all information returned by the resolver. In case of error, the function returns nil followed by an error message.

函数首先以一个字符串返回所给地址的主机名, 同时伴随着一个从解析器得到所有信息的表. 在出错的情况下, 函数返回 nil, 伴随着一个错误的信息.

## socket.dns.toip(address)

> Converts from host name to IPv4 address.

将一个主机名转化为 IPv4 地址.

> Address can be an IP address or host name.

address 参数能够是一个 IP 地址或者主机名.

> Returns a string with the first IP address found for address, followed by a table with all information returned by the resolver. In case of error, the function returns nil followed by an error message.

首先以字符串形式返回的是 address 地址对应的第一个 IP 地址, 同时伴随的还有从解析器获得所有信息的表. 在错误的情况下, 函数返回 nil, 并且伴随一个错误的信息.
