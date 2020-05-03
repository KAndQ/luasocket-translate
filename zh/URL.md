# URL

- [URL](#url)
    - [url.absolute(base, relative)](#urlabsolutebase-relative)
    - [url.build(parsed_url)](#urlbuildparsed_url)
    - [url.build_path(segments, unsafe)](#urlbuild_pathsegments-unsafe)
    - [url.escape(content)](#urlescapecontent)
    - [url.parse(url, default)](#urlparseurl-default)
    - [url.parse_path(path)](#urlparse_pathpath)
    - [url.unescape(content)](#urlunescapecontent)

> The  url namespace provides functions to parse, protect, and build URLs, as well as functions to compose absolute URLs from base and relative URLs, according to RFC 2396.

url 命名空间提供的功能是解析, 保护和创建 URLs, 也负责将基础和相对的 URLs 组合成绝对的 URLs, 符合 RFC 2396.

> To obtain the url namespace, run:

获得 url 命名空间, 运行如下代码:

```lua
-- loads the URL module
-- 加载 URL 模块
local url = require("socket.url")
```

> An URL is defined by the following grammar:

一个 URL 通过如下的语法定义:

```scheme
<url> ::= [<scheme>:][//<authority>][/<path>][;<params>][?<query>][#<fragment>]
<authority> ::= [<userinfo>@]<host>[:<port>]
<userinfo> ::= <user>[:<password>]
<path> ::= {<segment>/}<segment>
```

## url.absolute(base, relative)

> Builds an absolute URL from a base URL and a relative URL.

将一个基础的 URL 和相对的 URL 组合成一个绝对的 URL.

> Base is a string with the base URL or a parsed URL table.  Relative is a string with the relative URL.

如果 base 参数是一个字符串, 那么将是基础 URL; 或者是一个被解析的 URL 表. relative 参数是一个字符串, 用于相对 URL.

> The function returns a string with the absolute URL.

这个函数返回一个绝对的 URL 字符串.

> Note: The rules that govern the composition are fairly complex, and are described in detail in RFC 2396. The example bellow should give an idea of what the rules are.

注释: 组合管理的规则是相当复杂的, 具体的描述在 RFC 2396 中. 下面的例子给出了规则的概念.

```scheme
http://a/b/c/d;p?q

+

g:h      =  g:h
g        =  http://a/b/c/g
./g      =  http://a/b/c/g
g/       =  http://a/b/c/g/
/g       =  http://a/g
//g      =  http://g
?y       =  http://a/b/c/?y
g?y      =  http://a/b/c/g?y
#s       =  http://a/b/c/d;p?q#s
g#s      =  http://a/b/c/g#s
g?y#s    =  http://a/b/c/g?y#s
;x       =  http://a/b/c/;x
g;x      =  http://a/b/c/g;x
g;x?y#s  =  http://a/b/c/g;x?y#s
.        =  http://a/b/c/
./       =  http://a/b/c/
..       =  http://a/b/
../      =  http://a/b/
../g     =  http://a/b/g
../..    =  http://a/
../../   =  http://a/
../../g  =  http://a/g
```

## url.build(parsed_url)

> Rebuilds an URL from its parts.

从它的各个部分重新创建一个 URL.

> Parsed_url is a table with same components returned by parse. Lower level components, if specified, take precedence over high level components of the URL grammar.

parsed_url 参数是一个 table 类型, 和 parse 函数返回的相同部件组成. 如果指定了低级的组件, 那么它将取代 URL 高级语法的优先级.

> The function returns a string with the built URL.

这个函数返回一个创建好的 URL 字符串.

## url.build_path(segments, unsafe)

> Builds a <path> component from a list of <segment> parts. Before composition, any reserved characters found in a segment are escaped into their protected form, so that the resulting path is a valid URL path component.

从一个 <segment> 部件的列表构造一个 <路径> 组件. 在组合之前, 任何在 segment 中被保留的字符将被转义成它们的保护形式, 以便结果的路径是一个有效的 URL 路径组件.

> Segments is a list of strings with the <segment> parts. If unsafe is anything but nil, reserved characters are left untouched.

segments 参数是一个字符串列表, 使用于 <segment> 部分. 如果 unsafe 参数为任何值, 只要不为 nil, 保留的字符将保持不变.

> The function returns a string with the built <path> component.

这个函数返回一个字符串, 是被构造的 <路径> 组件.

## url.escape(content)

> Applies the URL escaping content coding to a string Each byte is encoded as a percent character followed by the two byte hexadecimal representation of its integer value.

对 URL 内容进行转义字符编码, 被编码的每个字符将转化成一个百分号紧跟两个十六进制的整型数字.

> Content is the string to be encoded.

content 参数是将要被编码的字符串内容.

> The function returns the encoded string.

函数返回编码后的字符串.

```lua
-- load url module
-- 加载 url 模块
url = require("socket.url")

code = url.escape("/#?;")
-- code = "%2f%23%3f%3b"
```

## url.parse(url, default)

> Parses an URL given as a string into a Lua table with its components.

将一个 URL 字符串解析为类似于它组件的 lua 表类型.

> Url is the URL to be parsed. If the default table is present, it is used to store the parsed fields. Only fields present in the URL are overwritten. Therefore, this table can be used to pass default values for each field.

url 是一个将要被解析的 url. 如果提供了 default 表, 它用于存储解析的字段. 只有字段出现在了 URL 中, 该字段才会被覆盖. 因此, 这个表能够用于给每个字段传递默认值.

> The function returns a table with all the URL components:

这个函数返回一个表, 包含所有的 URL 组件:

```lua
parsed_url = {
  url = string,
  scheme = string,
  authority = string,
  path = string,
  params = string,
  query = string,
  fragment = string,
  userinfo = string,
  host = string,
  port = string,
  user = string,
  password = string
}

-- load url module
url = require("socket.url")

parsed_url = url.parse("http://www.example.com/cgilua/index.lua?a=2#there")
-- parsed_url = {
--   scheme = "http",
--   authority = "www.example.com",
--   path = "/cgilua/index.lua"
--   query = "a=2",
--   fragment = "there",
--   host = "www.puc-rio.br",
-- }

parsed_url = url.parse("ftp://root:passwd@unsafe.org/pub/virus.exe;type=i")
-- parsed_url = {
--   scheme = "ftp",
--   authority = "root:passwd@unsafe.org",
--   path = "/pub/virus.exe",
--   params = "type=i",
--   userinfo = "root:passwd",
--   host = "unsafe.org",
--   user = "root",
--   password = "passwd",
-- }
```

## url.parse_path(path)

> Breaks a <path> URL component into all its <segment> parts.

拆分一个 <路径> URL 组件为它所有的 <segment> 部分.

> Path is a string with the path to be parsed.

path 参数是一个字符串类型, 将要被解析的路径.

> Since some characters are reserved in URLs, they must be escaped whenever present in a <path> component. Therefore, before returning a list with all the parsed segments, the function removes escaping from all of them.

因为一些字符在 URL 中被保留, 当它们需要在 <路径> 组件中显示时必须被转义编码. 因此, 在函数返回 segments 列表之前, 已经移除了转义编码.

## url.unescape(content)

> Removes the URL escaping content coding from a string.

移除字符串的转义编码内容.

> Content is the string to be decoded.

content 参数是被转义编码的字符串.

> The function returns the decoded string.

函数返回解码后的字符串.
