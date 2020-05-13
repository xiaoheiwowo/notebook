> HTTPS工作原理

- 一、首先HTTP请求服务端生成证书，客户端对证书的有效期、合法性、域名是否与请求的域名一致、证书的公钥（RSA加密）等进行校验；
- 二、客户端如果校验通过后，就根据证书的公钥的有效， 生成随机数，随机数使用公钥进行加密（RSA加密）；
- 三、消息体产生的后，对它的摘要进行MD5（或者SHA1）算法加密，此时就得到了RSA签名；
- 四、发送给服务端，此时只有服务端（RSA私钥）能解密。
- 五、解密得到的随机数，再用AES加密，作为密钥（此时的密钥只有客户端和服务端知道）。

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



## 首部字段

- Host: www.baidu.com
- Content-Length: 1000
- Connection: keep-alive Http/1.1默认都是持久连接

- Content-Type: text/html; charset=utf-8

- Accept: \*/\*

- Content-Encoding: gzip
- Accept-Encoding: gzip

# http

