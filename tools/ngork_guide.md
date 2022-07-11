## ngrok简介
```
是什么：
  ngrok是一个应用，可以在本地运行监控某个port生成url地址。

为什么需要：
  某些项目需要配合第三方的服务，而第三方的服务要求你提供可以调用的api接口。但是直接运行自己的server，生成的是本地或者
  局域网的url，此时就到了使用ngrok的时候。ngrok可运行监控本地的某个port，生成全网url，当接受到访问的请求，转发给
  本地的server。
```

### 场景分析
```
应用场景：
在项目开发过程中，需要向前端，或者其他应用开放接口进行测试，但是大家的IP不在同一个网段内，此时需要一个外部可访问的地址。

解决办法：
1. 布置到独立测试环境，以公网IP进行访问；

    优点：
    地址固定
    缺点：
    开发过程中修改麻烦，浪费IP

2. 使用ngrok

    优点：
    随时在本地搭建，使用方便
    缺点：
    免费的ngrok每次启动，url地址都不一样，如果前端或其他应用直接使用，一旦ngrok地址发生改变，需要多人多个地方进行改动
    解决办法：
    在公网的IP机器上搭建一个简单服务，把接收到的请求转接到本地的ngrok。前端或者第三方应用每次都使用固定的公网IP，即使ngrok生成的地址发生了改变，也只需要server开发到公网IP上简单修改一个ngrok地址。

```

### 使用方法
使用 ./ngrok -h 查看帮助
1：./ngrok http 8000
```
ngrok by @inconshreveable (Ctrl + C to quit)
Tunnel Status                 online
Update                        update available (version 2.1.3, Ctrl-U to update)
Version                       2.0.22/2.1.4       
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040   
Forwarding                    http://1fe84e10.ngrok.io -> localhost:8000  
Forwarding                    https://1fe84e10.ngrok.io -> localhost:8000
Connections                   ttl     opn     rt1     rt5     p50     p90
                            0       0       0.00    0.00    0.00    0.00
主要信息说明：
Web Interface: 本地浏览器输入http://127.0.0.1:4040，可以查看每次api请求，request，response的详细信息
Forwarding： 外部可访问的url地址 http://1fe84e10.ngrok.io [处理api请求，并转换到本地的8000端口]

```
