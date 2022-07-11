## 2017年04月12日

今天发现访问bamboohr api出现SSLError(e, request=request)\nSSLError: bad handshake: Error([('SSL routines', 'SSL3_GET_SERVER_CERTIFICATE', 'certificate verify failed')],)，测试服务器也开始出现同样异常。

问题不是必现，出现概率50%左右。

目前我们服务器操作系统是ubuntu 14.04，openssl版本为'OpenSSL 1.0.1f 6 Jan 2014'，python版本为2.7.6

下面是找到的一个类似案例
```
https://github.com/kennethreitz/requests/issues/3212

Aha, ok, we got there.

api.smartsheet.com serves its TLS using what's known as a "cross-signed certificate". This was used because Verisign, the CA for api.smartsheet.com, originally used a 1024-bit root certificate. These were deprecated and replaced by stronger root certificates, but some older browsers and systems may not have received updates, so sites like api.smartsheet.com serve a root certificate that is signed by the 1024-bit root.

That's not normally a problem, except:

certifi removed the weak 1024-bit roots
OpenSSL older than 1.0.2 sucks at building cert chains, and so fails to correctly validate the cross-signed root.
You can solve this in two ways. The first, better but more drastic way, is to upgrade your OpenSSL to 1.0.2 or later. This is hard to do on Centos, I'm afraid. The less good but more effective way is to get the output of running python -c "import certifi; print certifi.old_where()" and then set the REQUESTS_CA_BUNDLE environment variable to the printed path.
```

在测试服务器尝试增加REQUESTS_CA_BUNDLE环境变量，没有解决问题。

在本地使用Python 2.7.10/2.7.13测试，也出现同样问题。

## 2017年04月13日

没有改动的情况下，昨天的SSLError故障消失。
初步判定是bamboohr服务器证书配置有问题。
如何应对第三方服务故障，需要留意。