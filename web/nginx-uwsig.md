# nginx

1. 首先利用配置文件启动nginx。

   - 命令:` nginx -c /usr/local/nginx/conf/nginx.conf`

   - 重启服务：` service nginx restart`

2. 快速停止或关闭Nginx：`nginx -s stop`

3. 正常停止或关闭Nginx：`nginx -s quit`

4. 配置文件修改重装载命令：`nginx -s reload`



```nginx
# 配置一个server
server {
	listen <外网port>;
    server_name <name>;
    location <路径/url> {
    	proxy_pass <内网url>;
    }
    ...
}


```



# Uwsgi

```sh
# 命令
uwsgi --reload ini
uwsgi --stop pid
uwsgi --ini ini
```



```sh
# 配置文件 ini
[uwsgi]
http-socket = 127.0.0.1:5000

# 项目路径
chdir = <项目路径>
# 启动文件
wsgi-file = manager.py
# 程序内启用的application变量名
callable = app
# 处理器个数
processes = 2
# 线程个数
threads = 4
# 获取uwsgi统计信息的服务地址
stats = 127.0.0.1:5001

daemonize = <log路径及文件名>

pidfile = <pid路径及文件名> # 用于停止等

# 请求大小限制
buffer-size = 65535

# autoreload ?
```

