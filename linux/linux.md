```sh
# 将本机公钥传到服务器，
ssh-copy-id -i .ssh/id_rsa.pub -p 22 ubuntu@49.234.121.203
```

```sh
# 同步服务器时间
ntpdate ntp1.aliyun.com
```

```sh
# 查看占用空间最多的目录
du -sm * | sort -nr | sed 5q
du -h <file_name>
```

```sh
# 1. 添加 Python 的优先级
update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1   
update-alternatives --install /usr/bin/python python /usr/local/python3.6.3/bin/python3 2
# 2. 使用命令切换 Python 版本
update-alternatives --config python
```

```sh
netstat -lntup  
# 说明： l:listening   n:num   t:tcp  u:udp  p:process
ss
```

```sh
ps -ef |grep redis
-A 　显示所有程序。 
-e 　此参数的效果和指定"A"参数相同。
-f 　显示UID,PPIP,C与STIME栏位。 
```

```sh
# scp 传输文件
scp -r local_folder remote_username@remote_ip:remote_folder  
```

```sh
pip freeze > requirements.txt
pip install -r requirements.txt
```

```sh
mount / umount 挂载磁盘 卸载磁盘
sudo mkfs.ext4 /dev/sda1  格式化磁盘

# 1、手动挂载：
mount -t ntfs-3g /dev/sda1 /home/shares/public/disk1
# 2、开机挂载（vim /etc/fstab）
/dev/sda1 /home/shares/public/disk1 ntfs-3g rw,uid=1000,gid=1000,dmask=0002,fmask=0003 0 0
```

```sh
kill -9 4394
# kill就是给某个进程id发送了一个信号。默认发送的信号是SIGTERM，
# 而kill -9发送的信号是SIGKILL，即exit。exit信号不会被系统阻塞，
# 所以kill -9能顺利杀掉进程。当然你也可以使用kill发送其他信号给进程。
```

```sh
# 安装字体后执行
sudo fc-cache -fv
```

```sh
抓包 tcpdump -i lo port 23232 and host ...
查看端口状态 lsof -i tcp:23232
查看端口状态 ss
```

```sh
# 查看目录或文件大小
du -sh ...
```

```sh
nohup jupyter notebook --allow-root > jupyter.log 2>&1 &
screen -S jupyter
ctrl+a+d
screen -ls
screen -r

```
