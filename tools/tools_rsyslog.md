rsyslog 安装及配置
==================
# 1. 安装
```
sudo apt-get install rsyslog
```

# 2. 配置

1. 在 `/etc/rsyslog.conf` 中添加

```
$ModLoad imudp
$UDPServerRun 514
```

2. 创建目录 

```
mkdir -p /var/logs
```

3. 在 `/etc/rsyslog.d/` 中添加配置文件 `/etc/ryslog.d/50-abc.conf`

```
$template format, "%rawmsg%\n"
$template name,"/var/logs/appcloudbox.log"

:msg, contains, "flag"  -?name;format
```
