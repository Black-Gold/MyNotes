---
date:
  created: 2024-02-24
  updated: 2024-02-25
description: http状态码参考
categories:
  - Web
---

# HTTP状态码解释

[官方rfc7231文档参考](https://tools.ietf.org/html/rfc7231)

## HTTP状态码

```markdown
1xx（临时响应）:
  100 Continue（继续）：服务器已收到请求的初始部分，客户端应继续请求。
  101 Switching Protocols（切换协议）：服务器正在更改协议。
  102 Processing（处理中）：服务器已接受请求，但尚未完成处理。

2xx（成功）
    200 OK（成功）：请求已成功。
    201 Created（已创建）：请求已经被实现，而且有一个新的资源被创建。
    202 Accepted（已接受）：已接受请求，但尚未处理。
    203 Non-Authoritative Information（非权威信息）：服务器已成功处理了请求，但返回的信息可能来自另一来源。
    204 No Content（无内容）：服务器成功处理，但没有返回内容。
    205 Reset Content（重置内容）：服务器成功处理，用户代理应重置文档视图。
    206 Partial Content（部分内容）：服务器成功处理了部分GET请求。

3xx（重定向）
    300 Multiple Choices（多种选择）：对请求URI有多个可用资源。
    301 Moved Permanently（永久重定向）：请求的资源已被永久移动到新位置。
    302 Found（找到）：请求的资源临时从不同的URI响应。
    303 See Other（查看其他）：对于完成请求，客户端应该寻找另一个URI。
    304 Not Modified（未修改）：资源未被修改。
    305 Use Proxy（使用代理）：请求应使用代理访问请求的URI。
    307 Temporary Redirect（临时重定向）：对请求的资源临时从不同的URI响应。

4xx（请求错误）
    400 Bad Request（错误请求）：请求无效。
    401 Unauthorized（未授权）：需要身份验证。
    402 Payment Required（要求付款）：保留供将来使用。
    403 Forbidden（禁止）：服务器拒绝请求。
    404 Not Found（未找到）：服务器找不到请求的资源。
    405 Method Not Allowed（方法不允许）：请求中指定的方法不允许。
    406 Not Acceptable（不接受）：服务器生成的响应无法让客户端接受。
    407 Proxy Authentication Required（需要代理身份验证）：客户端必须先通过代理授权。
    408 Request Timeout（请求超时）：服务器等待请求时发生超时。
    409 Conflict（冲突）：请求导致冲突。
    410 Gone（消失）：请求的资源不再可用。
    411 Length Required（需要长度）：缺少必需的Content-Length头字段。
    412 Precondition Failed（前提条件失败）：请求中指定的前提条件失败。
    413 Payload Too Large（负载过大）：请求正文太大。
    414 URI Too Long（URI过长）：URI太长。
    415 Unsupported Media Type（不支持的媒体类型）：不支持请求中使用的媒体类型。
    416 Range Not Satisfiable（范围不可满足）：无法满足请求的Range头字段。
    417 Expectation Failed（期望失败）：服务器无法满足Expect头字段的期望值。
    426 Upgrade Required（需要升级）：客户端需要切换到TLS/1.0。

5xx（服务器错误）
    500 Internal Server Error（服务器内部错误）：服务器遇到了无法处理的错误。
    501 Not Implemented（未实现）：服务器不具备完成请求所需的功能。
    502 Bad Gateway（错误网关）：服务器作为网关或代理，从上游服务器接收到无效响应。
    503 Service Unavailable（服务不可用）：服务器暂时无法处理请求。
    504 Gateway Timeout（网关超时）：服务器作为网关或代理，未能及时从上游服务器接收到请求。
    505 HTTP Version Not Supported（HTTP版本不支持）：服务器不支持请求中所使用的HTTP协议版本。

[参考RFC 6585 - Additional HTTP Status Codes](https://tools.ietf.org/html/rfc6585)
RFC 6585 新增状态码
    418 I'm a teapot（我是一个茶壶）：服务器拒绝尝试用于冲泡咖啡的请求，因为它是一个茶壶。
    421 Misdirected Request（请求错误）：服务器无法生成对当前目标资源的响应，但可能对其他目标资源有效。
    428 Precondition Required（要求先决条件）：服务器要求请求包含前提条件。
    429 Too Many Requests（请求数过多）：用户在给定的时间内发送了太多请求。
    431 Request Header Fields Too Large（请求头字段太大）：服务器不愿意处理请求，因为请求头字段太大。
    451 Unavailable For Legal Reasons（因法律原因不可用）：请求的资源因法律原因不可用。
```
