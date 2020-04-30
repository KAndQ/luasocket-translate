# MIME API

- [MIME API](#mime-api)
    - [High-level filters(高级过滤器)](#high-level-filters高级过滤器)
        - [mime.normalize([marker])](#mimenormalizemarker)
        - [mine.decode](#minedecode)
        - [mine.encode](#mineencode)
        - [mime.stuff()](#mimestuff)
        - [mime.wrap](#mimewrap)
    - [Low-level filters(底层过滤器)](#low-level-filters底层过滤器)
        - [A, B = mime.b64(C [, D])](#a-b--mimeb64c--d)
        - [A, n = mime.dot(m [, B])](#a-n--mimedotm--b)
        - [A, B = mime.eol(C [, D, marker])](#a-b--mimeeolc--d-marker)
        - [A, B = mime.qp(C [, D, marker])](#a-b--mimeqpc--d-marker)
        - [A, m = mime.qpwrp(n [, B, length])](#a-m--mimeqpwrpn--b-length)
        - [A, B = mime.unb64(C [, D])](#a-b--mimeunb64c--d)
        - [A, B = mime.unqp(C [, D])](#a-b--mimeunqpc--d)
        - [A, m = mime.wrp(n [, B, length])](#a-m--mimewrpn--b-length)

> The mime namespace offers filters that apply and remove common content transfer encodings, such as Base64 and Quoted-Printable. It also provides functions to break text into lines and change the end-of-line convention. MIME is described mainly in RFC 2045, 2046, 2047, 2048, and2049.

mime 命名空间提供了一些筛选器, 用于应用和解除常见的传输编码, 如同, Base64 和 Quoted-Printable. 它也提供其他的功能, 将文本分解成行, 同时改变行结束符. MIME 主要在 RFC 2045, 2046, 2047, 2048, 2049 中有描述.

> All functionality provided by the MIME module follows the ideas presented in LTN012, Filters sources and sinks.

MIME 模块提供的功能都遵循 LTN012, Filters sources and sinks 文章中所提出的概念.

> To obtain the mime namespace, run:

获得 mime 命名空间, 执行下面代码:

```lua
-- loads the MIME module and everything it requires
-- 加载 MIME 模块和它所需的一切事物
local mime = require("mime")
```

## High-level filters(高级过滤器)

### mime.normalize([marker])

> Converts most common end-of-line markers to a specific given marker.

将最通常的行结束标记转化为指定的标记.

> Marker is the new marker. It defaults to CRLF, the canonic end-of-line marker defined by the MIME standard.

marker 参数是一个新的标记. 它默认是 CRLF, 标准的行结束标记已经在 MIME 标准中定义.

> The function returns a filter that performs the conversion.

这个函数返回一个执行转换的过滤器.

> Note: There is no perfect solution to this problem. Different end-of-line markers are an evil that will probably plague developers forever. This function, however, will work perfectly for text created with any of the most common end-of-line markers, i.e. the Mac OS (CR), the Unix (LF), or the DOS (CRLF) conventions. Even if the data has mixed end-of-line markers, the function will still work well, although it doesn't guarantee that the number of empty lines will be correct.

注解: 没有完美的解决方案解决这个问题. 不同的行结束标记是一个地狱, 它可能永远折磨着开发者. 不管怎样, 这个函数, 对于用许多常见的行结束标记创建的文本能够完美工作, 例如, 常规的 Mac OS(CR), Unix(LF), DOS(CRLF). 即使数据中混合着多种换行标记, 该函数也能很好的运行, 不过函数并不保证若干的空行会被还原.

### mine.decode

* mime.decode("base64")
* mime.decode("quoted-printable")

> Returns a filter that decodes data from a given transfer content encoding.

返回一个过滤器, 它将原来已经编码过的传输数据内容解码.

### mine.encode

* mime.encode("base64")
* mime.encode("quoted-printable" [, mode])

> Returns a filter that encodes data according to a given transfer content encoding.

返回一个过滤器, 它将将要传输的数据内容编码.

> In the Quoted-Printable case, the user can specify whether the data is textual or binary, by passing the mode strings "text" or "binary". Mode defaults to "text".

在使用 Quoted-Printable 的情况下, 用户能够指定的数据可以是文本也可以是二进制, 通过给 mode 传递字符串 "text" 或 "binary". mode 参数默认是 "text".

> Although both transfer content encodings specify a limit for the line length, the encoding filters do not break text into lines (for added flexibility). Below is a filter that converts binary data to the Base64 transfer content encoding and breaks it into lines of the correct size.

虽然两种传输数据编码格式能够指定行的长度限制, 但是过滤器不会将文本打碎成行(为的是增加灵活性). 下面这个过滤器将二进制数据进行 Base64 编码, 然后将它拆分多行, 每行都是一个释放的大小.

```lua
base64 = ltn12.filter.chain(
  mime.encode("base64"),
  mime.wrap("base64")
)
```

> Note: Text data has to be converted to canonic form before being encoded.

注释: 文本数据在编码前将换行符转换成标准的 CRLF.

```lua
base64 = ltn12.filter.chain(
  mime.normalize(),
  mime.encode("base64"),
  mime.wrap("base64")
)
```

### mime.stuff()

> Creates and returns a filter that performs stuffing of SMTP messages.

创建并且返回一个过滤器, 它执行 SMTP 消息的填充.

> Note: The smtp.send function uses this filter automatically. You don't need to chain it with your source, or apply it to your message body.

注释: smtp.send 函数自动的使用这个功能. 你不需要在你的代码中显示的调用, 或者将这个过滤器应用到你的消息正文中.

### mime.wrap

* mime.wrap("text" [, length])
* mime.wrap("base64")
* mime.wrap("quoted-printable")

> Returns a filter that breaks data into lines.

返回一个过滤器, 将数据拆分成多行.

> The "text" line-wrap filter simply breaks text into lines by inserting CRLF end-of-line markers at appropriate positions. Length defaults 76. The "base64" line-wrap filter works just like the default "text" line-wrap filter with default length. The function can also wrap "quoted-printable" lines, taking care not to break lines in the middle of an escaped character. In that case, the line length is fixed at 76.

"text" 行拆分过滤器只是简单的将 CRLF 行结束标记插入到文本的适当位置. length 参数默认是 76. "base64" 行拆分过滤器的工作原理类似 "text" 行拆分过滤器, 拆分长度为默认长度. 该函数也可以对 "quoted-printable" 进行换行, 不过要小心不要在转义字符间换行. 在这种情况下, 每行的长度固定是 76.

> For example, to create an encoding filter for the Quoted-Printable transfer content encoding of text data, do the following:

例如, 创建一个对文本数据进行 Quoted-Printable 编码的过滤器, 做如下的事情:

```lua
qp = ltn12.filter.chain(
  mime.normalize(),
  mime.encode("quoted-printable"),
  mime.wrap("quoted-printable")
)
```

> Note: To break into lines with a different end-of-line convention, apply a normalization filter after the line break filter.

注释: 因为有不同的行结束符标准拆分行, 所以可以在拆分行过滤器之后应用一个标准化的过滤器.

## Low-level filters(底层过滤器)

### A, B = mime.b64(C [, D])

> Low-level filter to perform Base64 encoding.

执行 Base64 编码的底层过滤器.

> A is the encoded version of the largest prefix of C..D that can be encoded unambiguously. B has the remaining bytes of C..D, before encoding. If D is nil, A is padded with the encoding of the remaining bytes of C.

A 是 C..D 的最大前缀编码版本, 它能够直接进行编码. B 保存着在 C..D 的编码前的字节大小. 如果 D 为 nil, A被填充为C的剩余字节编码.

> Note: The simplest use of this function is to encode a string into it's Base64 transfer content encoding. Notice the extra parenthesis around the call to mime.b64, to discard the second return value.

注释: 这个函数最简单的使用方式就是对一个字符串进行 Base64 编码. 注意, 在调用 mime.b64 函数时, 它周围的括号会忽略掉第二个返回值.

```lua
-- output: ZGllZ286cGFzc3dvcmQ=
print((mime.b64("diego:password")))
```

### A, n = mime.dot(m [, B])

> Low-level filter to perform SMTP stuffing and enable transmission of messages containing the sequence "CRLF.CRLF".

可以执行 SMTP 填充的底层过滤器, 允许传输的消息带有连续的 "CRLF.CRLF".

> A is the stuffed version of B. 'n' gives the number of characters from the sequence CRLF seen in the end of B. 'm' should tell the same, but for the previous chunk.

A 是 B 的填充版本. 'n' 给出的是在 B 的末尾, 连续出现 CRLF 字符的数量. 'm' 是相同的意思, 不过适用在先前的数据块 A.

> Note: The message body is defined to begin with an implicit CRLF. Therefore, to stuff a message correctly, the first m should have the value 2.

注释: 消息正文的开头隐式的定义一个 CRLF. 因此, 正确的填充消息, 第一个 m 参数的值应该是 2.

```lua
-- out put: ..\nStuffing the message.\n..\n..
print((string.gsub(mime.dot(2, ".\r\nStuffing the message.\r\n.\r\n."), "\r\n", "\\n")))
```

> Note: The smtp.send function uses this filter automatically. You don't need to apply it again.

注意: smtp.send 函数会自动的使用这个过滤器. 你也不需要手动的调用.

### A, B = mime.eol(C [, D, marker])

> Low-level filter to perform end-of-line marker translation. For each chunk, the function needs to know if the last character of the previous chunk could be part of an end-of-line marker or not. This is the context the function receives besides the chunk. An updated version of the context is returned after each new chunk.

执行行结束标记转化的底层 API. 对于每一个数据块, 函数需要知道前一个数据块的最后一个字符是否是行结束标记或者不是. 这是一个带有环境上下文的函数, 和接收到的数据块无关. 每一个新的上下文更新版本在每个新的数据块之后返回.

> A is the translated version of D. C is the ASCII value of the last character of the previous chunk, if it was a candidate for line break, or 0 otherwise. B is the same as C, but for the current chunk. Marker gives the new end-of-line marker and defaults to CRLF.

A 是 D 的转换版本. C 是前一个数据块的最后一个字符, 是一个 ASCII 值, 如果它是换行符的候选者, 或 0. B 是和是一样的, 只不过表示的是当前的数据块 A. maker 参数给予的是新的行结束标记, 默认为 CRLF.

```lua
-- translates the end-of-line marker to UNIX
-- 转换行结束标记为 UNIX 形式
unix = mime.eol(0, dos, "\n")
```

### A, B = mime.qp(C [, D, marker])

> Low-level filter to perform Quoted-Printable encoding.

执行 Quoted-Printable 编码的底层过滤器.

> A is the encoded version of the largest prefix of C..D that can be encoded unambiguously. B has the remaining bytes of C..D, before encoding. If D is nil, A is padded with the encoding of the remaining bytes of C. Throughout encoding, occurrences of CRLF are replaced by the marker, which itself defaults to CRLF.

A 是 C..D 的最大前缀编码版本, 它能够直接进行编码. B 存储 C..D 的字节数. 如果 D 是 nil, A被填充为 C 的剩余字节编码. 即使是编码, 也能够在 CRLF 出现的地方使用 maker 参数替换, 默认是 CRLF.

> Note: The simplest use of this function is to encode a string into it's Quoted-Printable transfer content encoding. Notice the extra parenthesis around the call to mime.qp, to discard the second return value.

注释: 使用这个函数最简单的方式就是讲一个字符串编码为它的 Quoted-Printable 内容. 注意, 在调用 qp 函数时, 它周围的括号会忽略掉第二个返回值.

```lua
-- output: ma=E7=E3=
print((mime.qp("maçã")))
```

### A, m = mime.qpwrp(n [, B, length])

> Low-level filter to break Quoted-Printable text into lines.

将 Quoted-Printable 文本拆分成行的底层过滤器.

> A is a copy of B, broken into lines of at most length bytes (defaults to 76). 'n' should tell how many bytes are left for the first line of B and 'm' returns the number of bytes left in the last line of A.

A 是 B 的一个副本, 拆分行的字节长度至少是 length(默认为 76). 'n' 参数应该告诉 B 的第一行还留下多少字节. 'm' 参数返回 A 最后一行留下的字节数.

> Note: Besides breaking text into lines, this function makes sure the line breaks don't fall in the middle of an escaped character combination. Also, this function only breaks lines that are bigger than length bytes.

注意: 除了将文本拆分成多行之外, 这个函数确保不会在转义字符组合之间拆分行. 另外, 这个函数只会拆分字节数比 length 参数大的行.

### A, B = mime.unb64(C [, D])

> Low-level filter to perform Base64 decoding.

执行 Base64 解码的底层过滤器.

> A is the decoded version of the largest prefix of C..D that can be decoded unambiguously. B has the remaining bytes of C..D, before decoding. If D is nil, A is the empty string and B returns whatever couldn't be decoded.

A 是 C..D 的最大前缀的解码版本, 它能够直接解码. B 存储的是 C..D 解码之前的字节数. 如果 D 为 nil, 这里的原文我认为有问题, 所以暂时不翻译.

> Note: The simplest use of this function is to decode a string from it's Base64 transfer content encoding. Notice the extra parenthesis around the call to mime.unb64, to discard the second return value.

注释: 这个函数最简单的使用方式就是对一个被 Base64 编码后的字符串直接进行解码. 注意, 在 mime.unb64 周围使用括号, 将忽略掉第二个返回值.

```lua
-- output: diego:password
print((mime.unb64("ZGllZ286cGFzc3dvcmQ=")))
```

### A, B = mime.unqp(C [, D])

> Low-level filter to remove the Quoted-Printable transfer content encoding from data.

从数据移除 Quoted-Printable 编码的底层 API.

> A is the decoded version of the largest prefix of C..D that can be decoded unambiguously. B has the remaining bytes of C..D, before decoding. If D is nil, A is augmented with the encoding of the remaining bytes of C.

A 是 C..D 的最大前缀的解码版本, 它能够直接的调用. B 是 C..D 解码之前的字节大小. 如果 D 为 nil, 这里的原文我认为有问题, 所以暂时不翻译.

> Note: The simplest use of this function is to decode a string from it's Quoted-Printable transfer content encoding. Notice the extra parenthesis around the call to mime.unqp, to discard the second return value.

注释: 这个函数最简单的使用方式就是对一个被 Quoted-Printable 编码后的字符串进行解码. 注意, 在 mime.unqp 周围使用括号, 将忽略掉第二个返回值.

```lua
-- output: maçã
print((mime.qp("ma=E7=E3=")))
```

### A, m = mime.wrp(n [, B, length])

> Low-level filter to break text into lines with CRLF marker. Text is assumed to be in the normalize form.

使用 CRLF 标记将文本拆分成多个行的过滤器. 文本被假定为 normalize 的形式.

> A is a copy of B, broken into lines of at most length bytes (defaults to 76). 'n' should tell how many bytes are left for the first line of B and 'm' returns the number of bytes left in the last line of A.

A 是 B 的一个副本, 拆分行的字节长度至少是 length(默认为 76). 'n' 参数应该告诉 B 的第一行还留下多少字节. 'm' 参数返回 A 最后一行留下的字节数.

> Note: This function only breaks lines that are bigger than length bytes. The resulting line length does not include the CRLF marker.

注释: 这个函数只会拆分比 length 字节数大的行. 最后一行的长度不包括 CRLF 标记符的长度.
