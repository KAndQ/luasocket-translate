# SMTP

> The smtp namespace provides functionality to send e-mail messages. The high-level API consists of two functions: one to define an e-mail message, and another to actually send the message. Although almost all users will find that these functions provide more than enough functionality, the underlying implementation allows for even more control (if you bother to read the code).

smtp 命名空间提供发送 e-mail 消息的功能. 高阶 API 由两个函数构成: 一个是定义一个 e-mail 消息, 另外一个是实际的发送消息. 虽然几乎所有的用户都会发现, 这些功能提供了足够的功能, 但是低阶的实现允许更多的控制(如果你有兴趣阅读代码的话).

> The implementation conforms to the Simple Mail Transfer Protocol, RFC 2821. Another RFC of interest is RFC 2822, which governs the Internet Message Format. Multipart messages (those that contain attachments) are part of the MIME standard, but described mainly in RFC 2046

当前的实现适用于简单邮件传输协议, RFC 2821. 另外一个有意思的 RFC 是 RFC 2822, 它统筹为互联网消息格式. 大部分的消息(包括附件)都是 MIME 标准的一部分, 不过主要的描述在 RFC 2046 里面.

> In the description below, good understanding of LTN012, Filters sources and sinks and the MIME module is assumed. In fact, the SMTP module was the main reason for their creation.

对于下面的描述, 假设您已经很好的理解了 LTN12 和 MIME 模块. 事实上, SMTP 模块是创作它们的主要原因.

> To obtain the smtp namespace, run:

获得 smtp 命名空间, 执行下面代码:

```lua
-- loads the SMTP module and everything it requires
-- 加载 SMTP 模块和它所需要的一切
local smtp = require("socket.smtp")
```

> MIME headers are represented as a Lua table in the form:

MIME 消息头在 lua 中以表的形式表示, 参考下面的表格:

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

字段名是大小写敏感的(指定的标准), 并且所有的功能对小写字段才有效(不过可以参考 socket.headers.canonic). 字段指保持不变.

> Note: MIME headers are independent of order. Therefore, there is no problem in representing them in a Lua table.

注释: MIME 消息头是无序的. 因此用 lua 的表来表示它们是没有问题的.

> The following constants can be set to control the default behavior of the SMTP module:

下面的常亮用来设置控制 SMTP 模块的默认行为:

> * DOMAIN: domain used to greet the server;
> * PORT: default port used for the connection;
> * SERVER: default server used for the connection;
> * TIMEOUT: default timeout for all I/O operations;
> * ZONE: default time zone.

* DOMAIN: 用来问候的服务器域名;
* PORT: 连接的默认端口;
* SERVER: 默认的连接服务器;
* TIMEOUT: I/O 操作的默认超时时间;
* ZONE: 默认的时区.

```lua
smtp.send {
  from = string,
  rcpt = string or string-table,
  source = LTN12 source,
  [user = string,]
  [password = string,]
  [server = string,]
  [port = number,]
  [domain = string,]
  [step = LTN12 pump step,]
  [create = function]
}
```

> Sends a message to a recipient list. Since sending messages is not as simple as downloading an URL from a FTP or HTTP server, this function doesn't have a simple interface. However, see the message source factory for a very powerful way to define the message contents.

发送一条信息到一个接收者列表. 因为发送的信息不像从 FTP 或者 HTTP 服务器下载的 URL那样简单, 所以这个函数没有简单的接口. 然而, 查看 message 工厂方法, 它是一个用来定义消息内容的有力方式.

> The sender is given by the e-mail address in the from field. Rcpt is a Lua table with one entry for each recipient e-mail address, or a string in case there is just one recipient. The contents of the message are given by a simple LTN12 source. Several arguments are optional:

发送者由 from 字段的 e-mail 地址表示. rcpt 可以是一个 lua 的表, 邮件接收者列表; 或者一个字符串, 表示只有一个接收者. 信息的内容通过 LTN12 的 source 给予. 其他可选的参数如下:

> * user, password: User and password for authentication. The function will attempt LOGIN and PLAIN authentication methods if supported by the server (both are unsafe);
> * server: Server to connect to. Defaults to "localhost";
> * port: Port to connect to. Defaults to 25;
> * domain: Domain name used to greet the server; Defaults to the local machine host name;
> * step: LTN12 pump step function used to pass data from the source to the server. Defaults to the LTN12 pump.step function;
> * create: An optional function to be used instead of socket.tcp when the communications socket is created.

* user, password: 认证的用户名和密码. 函数试图登录, 如果服务器支持简单的认证方法(这两者都不安全);
* server: 连接到的服务器. 默认是 "localhost";
* port: 连接的端口. 默认是 25;
* domain: 欢迎服务器的域名; 默认是本机的主机名;
* step: LTN12 pump step 功能, 用于将数据从 source 传递到服务器. 默认是 LTN12 的 pump.step 函数;
* create: 一个可选的函数, 用于代替建立连接时的 socket.tcp 函数.

> If successful, the function returns 1. Otherwise, the function returns nil followed by an error message.

如果成功, 这个函数将返回 1. 否则, 函数返回 nil, 并且伴随着错误信息.

> Note: SMTP servers can be very picky with the format of e-mail addresses. To be safe, use only addresses of the form "<fulano@example.com>" in the from and rcpt arguments to the send function. In headers, e-mail addresses can take whatever form you like.

注释: SMTP 服务器可能对于电子邮件格式非常的挑剔. 为了保证安全, 在 send 函数中 from 和 rcpt "<fulano@example.com>" 这种电子格式. 在消息头, 可以是任何你喜欢的电子邮件格式.

> Big note: There is a good deal of misconception with the use of the destination address field headers, i.e., the 'To', 'Cc', and, more importantly, the 'Bcc' headers. Do not add a 'Bcc' header to your messages because it will probably do the exact opposite of what you expect.

注意: 对于目标地址作用域消息头的使用有大量的误解, 也就是: 'To'(收件人), 'Cc'(抄送), 还有更重要的 'Bcc'(密送) 消息头 不要在你的消息中加入 'Bcc' 消息头, 因为它可能会做于你期望完全相反的事情.邮件基本尝试普及(to/cc/bcc)

> Only recipients specified in the rcpt list will receive a copy of the message. Each recipient of an SMTP mail message receives a copy of the message body along with the headers, and nothing more. The headers are part of the message and should be produced by the LTN12 source function. The rcpt list is not part of the message and will not be sent to anyone.

只有在收件人列表里面指明收件人, 这样才能接收到一个消息的副本. 每一个 SMTP 邮件消息接收者只会接收消息正文副本和消息头. 消息头是消息的一部分, 应该由 LTN12 的 source 函数生成. rcpt 列表不是消息的一部分, 它不会发送给任何人.

> RFC 2822 has two important and short sections, "3.6.3. Destination address fields" and "5. Security considerations", explaining the proper use of these headers. Here is a summary of what it says:

RFC 2822 有两个重要的简短章节, "3.6.3. 目标地址作用域" 和 "5. 安全注意事项", 解释了如何适当的使用这些消息头(to/cc/bcc). 这里有一些摘要:

> * To: contains the address(es) of the primary recipient(s) of the message;
> * Cc: (where the "Cc" means "Carbon Copy" in the sense of making a copy on a typewriter using carbon paper) contains the addresses of others who are to receive the message, though the content of the message may not be directed at them;
> * Bcc: (where the "Bcc" means "Blind Carbon Copy") contains addresses of recipients of the message whose addresses are not to be revealed to other recipients of the message.

* To: 包含消息主要接收者的地址;
* Cc: ("Cc" 的意思是 "抄送", 就像是在打字机上使用复写纸复制一样)包含消息接收人的地址, 尽管消息的内容可能不是针对他们的;
* Bcc: ("Bcc" 的意思是 "密送") 包含消息接收者的地址, 接收者的地址不透露给其他接收者知道.

> The LuaSocket send function does not care or interpret the headers you send, but it gives you full control over what is sent and to whom it is sent:

LuaSocket send 函数不关心或解释你发送的消息头, 但是它对于发送什么和谁发送的有完全的控制.

> * If someone is to receive the message, the e-mail address has to be in the recipient list. This is the only parameter that controls who gets a copy of the message;
> * If there are multiple recipients, none of them will automatically know that someone else got that message. That is, the default behavior is similar to the Bcc field of popular e-mail clients;
> * It is up to you to add the To header with the list of primary recipients so that other recipients can see it;
> * It is also up to you to add the Cc header with the list of additional recipients so that everyone else sees it;
> * Adding a header Bcc is nonsense, unless it is empty. Otherwise, everyone receiving the message will see it and that is exactly what you don't want to happen!

* 如果希望某人接收到消息, 那么这个人的电子邮件地址必须出现在接收者列表里. 这是唯一一个决定由谁获得消息副本的控制参数;
* 如果有多个接收者, 他们都不会自动的知道谁也获得了相同的消息. 换言之, 默认的行为就像是像在常用的电子邮件客户端的密送形式一样;
* 您可以在主要接收者列表里面加入 'To' 消息头, 这样其他的接收者可以互相知晓谁接收了这条消息;
* 您也可以在附加的接收者列表中增加 'Cc' 消息头, 这样被添加了 'Cc' 消息头的接收者知道其他接收这条消息的人;
* 添加 Bcc 消息头是无稽之谈, 除非这个邮件地址为空. 否则, 所有接收者都讲看到这个添加了 Bcc 消息头的接收者, 这正是你不想发生的事情.

> I hope this clarifies the issue. Otherwise, please refer to RFC 2821 and RFC 2822.

我希望这个问题能够阐明. 另外, 请参考 RFC 2821 和 RFC 2822.

```lua
-- load the smtp support
-- 加载 smtp 模块
local smtp = require("socket.smtp")

-- Connects to server "localhost" and sends a message to users
-- "fulano@example.com",  "beltrano@example.com",
-- and "sicrano@example.com".
-- Note that "fulano" is the primary recipient, "beltrano" receives a
-- carbon copy and neither of them knows that "sicrano" received a blind
-- carbon copy of the message.

-- 连接服务器 "localhost", 并且发送消息给用户
-- "fulano@example.com",  "beltrano@example.com", "sicrano@example.com".
-- 注意, "fulano" 是主要的接收者, "beltrano" 接收一个抄送副本,
-- 他们都不知道 "sicrano" 接收一个密送的消息副本.
from = "<luasocket@example.com>"

rcpt = {
  "<fulano@example.com>",
  "<beltrano@example.com>",
  "<sicrano@example.com>"
}

mesgt = {
  headers = {
    to = "Fulano da Silva <fulano@example.com>",
    cc = '"Beltrano F. Nunes" <beltrano@example.com>',
    subject = "My first message"
  },
  body = "I hope this works. If it does, I can send you another 1000 copies."
}

r, e = smtp.send{
  from = from,
  rcpt = rcpt,
  source = smtp.message(mesgt)
}
```

## smtp.message(mesgt)

> Returns a simple LTN12 source that sends an SMTP message body, possibly multipart (arbitrarily deep).

返回一个简单的 LTN12 source 对象, 它发送一个 SMTP 消息正文, 可能是复合的(任意深度).

> The only parameter of the function is a table describing the message. Mesgt has the following form (notice the recursive structure):

函数只有一个参数, 使用一个表来表示消息. mesgt 参数的形式如下(注意递归结构):

```lua
mesgt = {
  headers = header-table,
  body = LTN12 source or string or multipart-mesgt
}

multipart-mesgt = {
  [preamble = string,]
  [1] = mesgt,
  [2] = mesgt,
  ...
  [n] = mesgt,
  [epilogue = string,]
}
```

> For a simple message, all that is needed is a set of headers and the body. The message body can be given as a string or as a simple LTN12source. For multipart messages, the body is a table that recursively defines each part as an independent message, plus an optional preamble and epilogue.

对于简单的消息, 需要配置的就是 headers 和 body 字段. body 能够是一个字符串, 或者一个简单的 LTN12 的 source. 对于复合消息, body 是一个表, 它递归的将每个信息作为独立的部分定义, 加上一个可选项 preamble 和 epilogue.

> The function returns a simple LTN12 source that produces the message contents as defined by mesgt, chunk by chunk. Hopefully, the following example will make things clear. When in doubt, refer to the appropriate RFC as listed in the introduction.

函数返回一个简单的 LTN12 的 source, 它通过 mesgt  参数生成消息的文本内容, 一个接一个的消息块. 希望下面的例子能够让事情变得简单一些. 当疑惑的时候, 参考上面介绍中列出的 RFC.

```lua
-- load the smtp support and its friends
-- 加载 smtp 和它相关联的模块
local smtp = require("socket.smtp")
local mime = require("mime")
local ltn12 = require("ltn12")

-- creates a source to send a message with two parts. The first part is
-- plain text, the second part is a PNG image, encoded as base64.
-- 创建一个 source 和发送消息的两个部分.
-- 第一个部分是简单的文本,
-- 第二个部分是一个 png 图片, 使用 base64 编码.
source = smtp.message{
  headers = {
     -- Remember that headers are *ignored* by smtp.send.
     -- 记住 headers 字段被 smtp.send 函数 *忽略*
     from = "Sicrano de Oliveira <sicrano@example.com>",
     to = "Fulano da Silva <fulano@example.com>",
     subject = "Here is a message with attachments"
  },
  body = {
    preamble = "If your client doesn't understand attachments, \r\n" ..
               "it will still display the preamble and the epilogue.\r\n" ..
               "Preamble will probably appear even in a MIME enabled client.",
    preamble = "如果您的客户端不明白附件, \r\n" ..
               "客户端仍将显示 preamble 和 epilogue 的内容. \r\n" ..
               "即使在 MIME 开启的客户端, Preamble 也有可能呈现."

    -- first part: no headers means plain text, us-ascii.
    -- The mime.eol low-level filter normalizes end-of-line markers.
    -- 第一部分: 标有标题头意味着简单文本, us-ascii 编码.
    -- mime.eol 低阶过滤器, 标准化行结束标记.
    [1] = {
      body = mime.eol(0, [[
        Lines in a message body should always end with CRLF.
        The smtp module will *NOT* perform translation. However, the
        send function *DOES* perform SMTP stuffing, whereas the message
        function does *NOT*.
      ]])

      body = mime.eol(0, [[
        在消息正文内的行结束标记永远是 CRLF.
        smtp 模块将 *不会* 执行转化. 然而, send 函数将 *执行* SMTP 填充, 而不是 message 函数.
      ]])
     },

    -- second part: headers describe content to be a png image,
    -- sent under the base64 transfer content encoding.
    -- notice that nothing happens until the message is actually sent.
    -- small chunks are loaded into memory right before transmission and
    -- translation happens on the fly.
    -- 第二部分: 消息头描述内容是一个 png 图片,
    -- 发送的传输内容经过 base64 编码.
    -- 注意, 知道信息被实际发送, 否则什么也不会发生.
    -- 在传输和编码转化发生之前, 小的数据块被加载到内存中.
    [2] = {
      headers = {
        ["content-type"] = 'image/png; name="image.png"',
        ["content-disposition"] = 'attachment; filename="image.png"',
        ["content-description"] = 'a beautiful image',
        ["content-transfer-encoding"] = "BASE64"
      },
      body = ltn12.source.chain(
        ltn12.source.file(io.open("image.png", "rb")),
        ltn12.filter.chain(
          mime.encode("base64"),
          mime.wrap()
        )
      )
    },
    epilogue = "This might also show up, but after the attachments",
    epilogue = "这个有可能会显示, 不过是在附件之后"
  }
}

-- finally send it
-- 最后将数据发送出去
r, e = smtp.send{
    from = "<sicrano@example.com>",
    rcpt = "<fulano@example.com>",
    source = source,
}
```
