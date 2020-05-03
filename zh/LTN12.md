# LTN12

- [LTN12](#ltn12)
    - [Filters](#filters)
        - [ltn12.filter.chain(filter1, filter2 [, ... filterN])](#ltn12filterchainfilter1-filter2---filtern)
        - [ltn12.filter.cycle(low [, ctx, extra])](#ltn12filtercyclelow--ctx-extra)
    - [Pumps](#pumps)
        - [ltn12.pump.all(source, sink)](#ltn12pumpallsource-sink)
        - [ltn12.pump.step(source, sink)](#ltn12pumpstepsource-sink)
    - [Sinks](#sinks)
        - [ltn12.sink.chain(filter, sink)](#ltn12sinkchainfilter-sink)
        - [ltn12.sink.null()](#ltn12sinknull)
        - [ltn12.sink.simplify(sink)](#ltn12sinksimplifysink)
        - [ltn12.sink.table([table])](#ltn12sinktabletable)
    - [Sources](#sources)
        - [ltn12.source.cat(source1 [, source2, ..., sourceN])](#ltn12sourcecatsource1--source2--sourcen)
        - [ltn12.source.chain(source, filter)](#ltn12sourcechainsource-filter)
        - [ltn12.source.empty()](#ltn12sourceempty)
        - [ltn12.source.error(message)](#ltn12sourceerrormessage)
        - [ltn12.source.file(handle, message)](#ltn12sourcefilehandle-message)
        - [ltn12.source.simplify(source)](#ltn12sourcesimplifysource)
        - [ltn12.source.string(string)](#ltn12sourcestringstring)

[参考](http://wenku.baidu.com/view/9f5e31100b4e767f5acfce1c)
[参考](./doc/luasocket学习ltn12中文版.doc)

我的简单理解 LTN12. 首先有几个概念 filter, chain, source, sink, pumps.
source 是数据源, 数据从这里来;
sink 是数据目的地, 数据最终会到达这里;
filter 数据操作封装在 filter 中, 例如编码, 加密等等;
chain 可以理解为是将多个 filter 组成一个链, 一组数据可以进行多次的 filter 的数据操作;
pumps 将 source 的数据通过 chain 里面的 filter, 全部注入到 sink 中.

以下是翻译:

> The ltn12 namespace implements the ideas described in LTN012, Filters sources and sinks. This manual simply describes the functions. Please refer to the LTN for a deeper explanation of the functionality provided by this module.

ltn12 命名空间的实现想法在 LTN012, Filters sources and sinks 中有介绍. 当前这个手册会简单的描述函数功能. 请参考 LTN 能够对本模块的功能有更深层次的了解.

> To obtain the ltn12 namespace, run:

得到 ltn12 命名空间运行如下, 代码:

```lua
-- loads the LTN12 module
-- 加载 LTN12 模块
local ltn12 = require("ltn12")
```

## Filters

### ltn12.filter.chain(filter1, filter2 [, ... filterN])

> Returns a filter that passes all data it receives through each of a series of given filters.

返回一个 filter 对象, 它接收所有的数据, 并将这些数据穿过一系列给定的过滤器.

> Filter1 to filterN are simple filters.

Filter1 to filterN 参数是一些简易的过滤器.

> The function returns the chained filter.

这个函数返回一个过滤器的链表.

> The nesting of filters can be arbitrary. For instance, the useless filter below doesn't do anything but return the data that was passed to it, unaltered.

可以是任意形式的嵌套过滤器. 例如, 下面的例子中的无效过滤器不做任何事情, 但是数据仍然穿过过滤器, 只是数据不会被改变.

```lua
-- load required modules
-- 加载需要的模块
local ltn12 = require("ltn12")
local mime = require("mime")

-- create a silly identity filter
-- 创建一个数据不会改变的过滤器
id = ltn12.filter.chain(
  mime.encode("quoted-printable"),
  mime.encode("base64"),
  mime.decode("base64"),
  mime.decode("quoted-printable")
)
```

### ltn12.filter.cycle(low [, ctx, extra])

> Returns a high-level filter that cycles though a low-level filter by passing it each chunk and updating a context between calls.

返回一个高级的过滤器, 后面的哥目前实在翻译不出来....

> Low is the low-level filter to be cycled, ctx is the initial context and extra is any extra argument the low-level filter might take.

low 参数是构成循环的低级过滤器, ctx 是一个初始化上下文, extra 参数是任意一个可能传递给低级过滤器的参数.

> The function returns the high-level filter.

这个函数返回一个高级的过滤器.

```lua
-- load the ltn12 module
-- 加载 ltn12 模块
local ltn12 = require("ltn12")

-- the base64 mime filter factory
-- base64 mime 过滤器工厂
encodet['base64'] = function()
    return ltn12.filter.cycle(b64, "")
end
```

## Pumps

### ltn12.pump.all(source, sink)

> Pumps all data from a source to a sink.

将所有的数据从 source 输送到 sink.

> If successful, the function returns a value that evaluates to true. In case of error, the function returns a false value, followed by an error message.

如果成功, 这个函数返回 true. 在错误的情况下, 函数返回 false, 并且伴随着错误信息.

### ltn12.pump.step(source, sink)

> Pumps one chunk of data from a source to a sink.

将数据的一个数据块从 source 输送到 sink.

> If successful, the function returns a value that evaluates to true. In case of error, the function returns a false value, followed by an error message.

如果成功, 这个函数返回 true. 在错误的情况下, 函数返回 false, 并且伴随着错误信息.

## Sinks

### ltn12.sink.chain(filter, sink)

> Creates and returns a new sink that passes data through a filter before sending it to a given sink.

创建并且返回一个新的 sink, 数据在到达给定的 sink(参数) 之前将首先通过 filter.

### ltn12.sink.null()

> Returns a sink that ignores all data it receives.

返回一个 sink, 它将忽略所有的接收到的数据.

### ltn12.sink.simplify(sink)

> Creates and returns a simple sink given a fancy sink.

将一个精心设计的 sink 作为参数, 创建并且返回一个简单的 sink.

### ltn12.sink.table([table])

> Creates a sink that stores all chunks in a table. The chunks can later be efficiently concatenated into a single string.

创建一个 sink, 它将所有的数据块都存储在 table 里面. 这些数据块以后可以高效连接成一个单一的字符串.

> Table is used to hold the chunks. If nil, the function creates its own table.

Table 参数用于保存数据块. 如果为 nil, 这个函数创建一个自己的 table.

> The function returns the sink and the table used to store the chunks.

这个函数返回 sink 和用于存储数据块的 table.

```lua
-- load needed modules
-- 加载需要的模块
local http = require("socket.http")
local ltn12 = require("ltn12")

-- a simplified http.get function
-- 一个简单的 http.get 函数
function http.get(u)
  local t = {}
  local respt = request{
    url = u,
    sink = ltn12.sink.table(t)
  }
  return table.concat(t), respt.headers, respt.code
end
```

## Sources

### ltn12.source.cat(source1 [, source2, ..., sourceN])

> Creates a new source that produces the concatenation of the data produced by a number of sources.

创建一个新的 source, 该 source 能够拼接由若干个 source 产生的数据.

> Source1 to sourceN are the original sources.

Source1 to sourceN 是最初的 source.

> The function returns the new source.

这个函数返回一个新的 source.

### ltn12.source.chain(source, filter)

> Creates a new source that passes data through a filter before returning it.

创建一个新的 source, 数据在返回之前将穿过 filter.

> The function returns the new source.

这个函数将返回新的 source.

### ltn12.source.empty()

> Creates and returns an empty source.

创建并且返回一个空的 source.

### ltn12.source.error(message)

> Creates and returns a source that aborts transmission with the error message.

创建并且返回一个 source, 它终止传输错误的信息.

### ltn12.source.file(handle, message)

> Creates a source that produces the contents of a file.

创建一个 source, 产生文件的内容.

> Handle is a file handle. If handle is nil, message should give the reason for failure.

handle 参数是一个文件的句柄. 如果 handle 为 nil, message 参数应该给出失败的原因.

> The function returns a source that reads chunks of data from given handle and returns it to the user, closing the file when done, or a source that aborts the transmission with the error message

这个函数返回一个 source, 它会从给定的 handle 读取数据块, 并且返回给用户, 在读取完毕之后关闭文件, 或者 source 终止传输错误的信息.

> In the following example, notice how the prototype is designed to fit nicely with the io.open function.

在下面的例子中, 介绍了如何精确使用 io.open 函数的设计原型.

```lua
-- load the ltn12 module
-- 加载 ltn12 模块
local ltn12 = require("ltn12")

-- copy a file
-- 复制一个文件
ltn12.pump.all(
  ltn12.source.file(io.open("original.png", "rb")),
  ltn12.sink.file(io.open("copy.png", "wb"))
)
```

### ltn12.source.simplify(source)

> Creates and returns a simple source given a fancy source.

给定一个精心设计的 source 作为参数, 返回一个简易的 source.

### ltn12.source.string(string)

> Creates and returns a source that produces the contents of a string, chunk by chunk.

创建并且返回一个 source, 该 source 处理字符串的内容, 生成一个个数据块.
