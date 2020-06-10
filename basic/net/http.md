

## 状态码

- 1xx 提示信息
- 2xx 成功状态
    - 200 OK
    - 204 No Content 成功,不包含body
    - 206 Partail Content 成功, 只包含部分数据
- 3xx 重定向
    - 301 Moved Permanently 永久重定向
    - 302 Moved Permanently 临时重定向 新的url会包含在Location中
    - 304 Not Modefied 缓存重定向,表示资源未修改,缓存有效
- 4xx 报文有误,客户端错误
    - 400 Bad Request 报文有错误
    - 403 Forbidden 禁止访问
    - 404 Not Found 没有
- 5xx 服务器错误
    - 500 Internal Server Error 服务器错误
    - 501 Not Implemented 表示客户端请求的功能还不支持
    - 502 Bad Gateway 服务器正常, 后端程序挂了
    - 503 Service Unavailable 服务器忙



## 首部字段(通用标头`、`实体标头`、`请求标头`、`响应标头)

通用标头主要有三个，分别是 `Date`、`Cache-Control` 和 `Connection`

实体标头是描述消息正文内容的 HTTP 标头。实体标头用于 HTTP 请求和响应中。头部`Content-Length`、 `Content-Language`、 `Content-Encoding` 是实体头。

请求 host **Accept**，**Accept-Charset**，**Accept-Language**

响应**Access-Control-Allow-Origin**，**Keep-Alive**，**Set-Cookie**	

- Host: www.baidu.com
- Content-Length: 1000
- Connection: keep-alive Http/1.1默认都是持久连接

- Content-Type: text/html; charset=utf-8

- Accept: \*/\*

- Content-Encoding: gzip
- Accept-Encoding: gzip

## http1.1

- 摘要认证
- 长连接
- 断点续传

## http2.0

- 头部压缩
- 二进制
- 多路复用
- 数据帧发送

 ## http3.0

- 基于QUIC（基于UDP） 快

## https

![](/Users/xiaohei/private/notebook/pic/v2-78cbd8ad976a559130e9079bdc40a5c7_b.jpg)

- 在进行通信前，首先会进行 HTTP 的三次握手，握手完成后，再进行 TLS 的握手过程
- ClientHello：客户端通过向服务器发送 `hello` 消息来发起握手过程。这个消息中会夹带着客户端支持的 `TLS 版本号(TLS1.0 、TLS1.2、TLS1.3)` 、客户端支持的密码套件、以及一串 `客户端随机数`。
- ServerHello：在客户端发送 hello 消息后，服务器会发送一条消息，这条消息包含了服务器的 SSL 证书、服务器选择的密码套件和服务器生成的随机数。
- 认证(Authentication)：客户端的证书颁发机构会认证 SSL 证书，然后发送 `Certificate` 报文，报文中包含公开密钥证书。最后服务器发送 `ServerHelloDone` 作为 `hello` 请求的响应。第一部分握手阶段结束。
- `加密阶段`：在第一个阶段握手完成后，客户端会发送 `ClientKeyExchange` 作为响应，这个响应中包含了一种称为 `The premaster secret` 的密钥字符串，这个字符串就是使用上面公开密钥证书进行加密的字符串。随后客户端会发送 `ChangeCipherSpec`，告诉服务端使用私钥解密这个 `premaster secret` 的字符串，然后客户端发送 `Finished` 告诉服务端自己发送完成了。

Session key 其实就是用公钥证书加密的公钥。

- `实现了安全的非对称加密`：然后，服务器再发送 `ChangeCipherSpec` 和 `Finished` 告诉客户端解密完成，至此实现了 RSA 的非对称加密。

- HTTPS工作原理
  - 一、首先HTTP请求服务端生成证书，客户端对证书的有效期、合法性、域名是否与请求的域名一致、证书的公钥（RSA加密）等进行校验；
  - 二、客户端如果校验通过后，就根据证书的公钥的有效， 生成随机数，随机数使用公钥进行加密（RSA加密）；
  - 三、消息体产生的后，对它的摘要进行MD5（或者SHA1）算法加密，此时就得到了RSA签名；
  - 四、发送给服务端，此时只有服务端（RSA私钥）能解密。
  - 五、解密得到的随机数，再用AES加密，作为密钥（此时的密钥只有客户端和服务端知道）。



## 幂等性

- 前端限制
- 数据库唯一约束：通过参数生成订单号，加入数据库
- 状态机制：处理数据前判断状态
- token机制：请求一个全局唯一token放到缓存，正常处理先检查token是否存在，最后删除token

