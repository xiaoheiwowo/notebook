## 目的
```
1. nginx的简单实用
2. 能够满足基本的线上业务的需求应用
```

## nginx介绍

### nginx的基本介绍
```
1. Nginx是一个跨平台的Web服务器，可以使用当前操作系统特有的一些高效API来提高自己的性能
2. Nginx由一个master进程和若干worker进程组成，master进程主要用于读取配置文件和管理worker进程.worker进程负责处理请求。nginx基于事件模型并且能够根据操作系统的特性高效的利用worker进程处理请求。worker进程的数量在配置文件中定义，也可以通过指定的配置文件来定义其数量，或者自动根据CPU核心数确定其数量。

```

### nginx的使用
####  nginx的安装、启动、...
```
1. 安装
  sudo apt-get update;sudo apt-get intall Nginx;
2. 使用
  测试： sudo service nginx configtest 配置文件是否ok//查看帮助，sudo service nginx -t
        sudo ngixn -t 测试每一个配置文件

  重启： sudo service nginx reload
  stop: sudo service nginx stop
  start: sudo service nginx start / sudo service nginx restart

```
####  nginx的配置、语法
参考资料
```
1. http://ifool.me/linux/494.html
2. https://www.linode.com/docs/websites/nginx/how-to-configure-nginx
```

####  nginx的配置
##### 基本介绍
location模块
http://seanlook.com/2015/05/17/nginx-location-rewrite/
```
1. 以 = 开头表示精确匹配，如上面的url链接只匹配根目录结尾的请求，后面不能带任何字符串。
2. ^~ 开头表示uri以某个常规字符串开头，不是正则匹配
3. ~ 开头表示区分大小写的正则匹配
4. ~* 开头表示不区分大小写的正则匹配
5. / 通用匹配，如果没有其它匹配，任何请求都会匹配到。
```

示例如下
转自 [Sean's Notes](http://seanlook.com/2015/05/17/nginx-location-rewrite/)
```
location  = / {
  # 精确匹配 / ，主机名后面不能带任何字符串
  [ configuration A ]
}
location  / {
  # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
  # 但是正则和最长字符串会优先匹配
  [ configuration B ]
}
location /documents/ {
  # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration C ]
}
location ~ /documents/Abc {
  # 匹配任何以 /documents/Abc 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration CC ]
}
location ^~ /images/ {
  # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
  [ configuration D ]
}
location ~* \.(gif|jpg|jpeg)$ {
  # 匹配所有以 gif,jpg或jpeg 结尾的请求
  # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
  [ configuration E ]
}
location /images/ {
  # 字符匹配到 /images/，继续往下，会发现 ^~ 存在
  [ configuration F ]
}
location /images/abc {
  # 最长字符匹配到 /images/abc，继续往下，会发现 ^~ 存在
  # F与G的放置顺序是没有关系的
  [ configuration G ]
}
location ~ /images/abc/ {
  # 只有去掉 config D 才有效：先最长匹配 config G 开头的地址，继续往下搜索，匹配到这一条正则，采用
    [ configuration H ]
}
```

```
已=开头表示精确匹配
如 A 中只匹配根目录结尾的请求，后面不能带任何字符串。
^~ 开头表示uri以某个常规字符串开头，不是正则匹配
~ 开头表示区分大小写的正则匹配;
~* 开头表示不区分大小写的正则匹配
/ 通用匹配, 如果没有其它匹配,任何请求都会匹配到
```

```
顺序 优先级：
(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)
```

### nginx注意事项（实际的解决问题）
1. elb的check的检查
elb请求index.html,可能会大量产生404的请求，可以在nginx的location模块进行配置
```
location =/index.html {
  return 200 'ok\n';
}
```
2. proxy 转向
```
location /api {
  include proxy_params;
  proxy_pass http://127.0.0.1:8100;
}
```
3. gzip 打开
```
gzip on;
```
4. 文件大小的限制
```
client_max_body_size 50M;
```
5. 包含其他的配置文件
```
include /etc/nginx/conf.d/*.conf;
```

6. 监听域名
```
location {
  listen 80 default_server;
  server_name localhost slack.ihandysoft.com;
}
```
