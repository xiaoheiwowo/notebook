```sh
# get-pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
# ohmyzsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# vim-plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

```sh
# 将本机公钥传到服务器，
ssh-copy-id (-i .ssh/id_rsa.pub -p 22) ubuntu@49.234.121.203

ssh -t root@host 'ls'



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

```shell
# grep '^root$'
-A 10 # 后10行
-B 10 # 前10行
-C 10 # 前后10行
-n # 行号
-i # 大小写敏感
-v # 排除
--color=auto # 高亮
# cut 列截取
-d: # 指定分隔符 
-f # 截取列
-c # 字符截取
```



```
# 时间戳
$(date +%s)
```

```
# vim 替换部分行
.,+2s/abc/helloworld/g
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
HUP 1 终端断线
INT 2 中断（同 Ctrl + C）
QUIT 3 退出（同 Ctrl + \）
TERM 15 终止 默认
KILL 9 强制终止
CONT 18 继续（与STOP相反， fg/bg命令）
STOP 19 暂停（同 Ctrl + Z）
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
 lsof -i:5000
查看端口状态 ss

sudo ngrep -d any -q -Wbyline . port 8125
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

## 压缩

```sh
# 解压缩
xz -d ...
tar -xvf ...

tar xvJf ...
# 压缩
xz -z ...
tar -cvf ...


tar
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出

下面的参数-f是必须的

-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

# tar -cf all.tar *.jpg 
这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。 

# tar -rf all.tar *.gif 
这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。 

# tar -uf all.tar logo.gif 
这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。 

# tar -tf all.tar 
这条命令是列出all.tar包中所有文件，-t是列出文件的意思 

# tar -xf all.tar 
这条命令是解出all.tar包中所有文件，-x是解开的意思 

压缩
tar –cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg
tar –czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for Linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux

解压
tar –xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar –xZvf file.tar.Z   //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip

总结
1、*.tar 用 tar –xvf 解压
2、*.gz 用 gzip -d或者gunzip 解压
3、*.tar.gz和*.tgz 用 tar –xzf 解压
4、*.bz2 用 bzip2 -d或者用bunzip2 解压
5、*.tar.bz2用tar –xjf 解压
6、*.Z 用 uncompress 解压
7、*.tar.Z 用tar –xZf 解压
8、*.rar 用 unrar e解压
9、*.zip 用 unzip 解压

原文链接：https://blog.csdn.net/learn8more/article/details/86666349
```

## curl

```shell
curl -v/-s/-S/--trace/--trace-ascii(保存文件) <file> -H "Content-Type: application/json"(-G 转为GET）http://www.baidu.com -d/-F "key=value" -o <file>
```



```sh
curl 'https://www.baidu.com/' -H 'Connection: keep-alive' -H 'Cache-Control: max-age=0' -H 'Upgrade-Insecure-Requests: 1' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.97 Safari/537.36' -H 'Sec-Fetch-User: ?1' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-Mode: navigate' -H 'Accept-Encoding: gzip, deflate, br' -H 'Accept-Language: zh-CN,zh;q=0.9,en;q=0.8' -H 'Cookie: BIDUPSID=2ED602F831C9B952E536FE0ED2607203; PSTM=1568875905; BD_UPN=123253; BAIDUID=7EC8F1FEED6A913FFE786732135F3735:FG=1; BDUSS=WI2WE95SHFzelhxc0Y3UXhIOUVXVEdDUlBzZ1NWUXQya2cyMUpnN3VJMWhBNzFkRUFBQUFBJCQAAAAAAAAAAAEAAADrTpcJNzI0Njg2OTY0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGF2lV1hdpVdO; MCITY=-332%3A; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; delPer=0; BD_CK_SAM=1; PSINO=1; BD_HOME=1; session_id=1573779262555; session_name=www.baidu.com; H_PS_PSSID=1451_21109_20698_29568_29700_29220_26350; H_PS_645EC=130bsjAIWB%2F%2F3HE40pBZQlObps1%2B55xUUCQkqop9Ikm%2FwO0Qcv5Bx3yyFGs' --compressed
```

## manjaro

```shell
# 1. 测试国内的镜像源，测试完之后会在一个指定的文件中生成镜像列表
sudo pacman-mirrors -i -c China -m rank
# 2. 设置archlinuxcn源
sudo vim /etc/pacman.conf
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
# 3.更新源列表
sudo pacman-mirrors -g
# 4. 更新pacman数据库并全面更新系统
sudo pacman -Syyu
```



### awk

```shell
awk [options] "pattern {action}" file
awk -F: '/root/ {print $1}'  /etc/passwd
# 字段，字符分隔符FS，记录，字段数量NF，记录数量NR，记录分隔符RS, 输出字段分隔符OFS, 输出记录分隔符ORS
root:x:0:0:root:/root:/bin/bash \n

awk -F ':[<' '{ NR % 2 == 1 }  {print NR ") " toupper($1)}' /etc/passwd
```

```
wc -l 统计行数
awk -F: '/root/ {print $1}'  /etc/passwd | wc -l
```



```
centos网络相关设置文件 /etc/sysconfig/network-scripts/ifcfg-ens33
```





```shell
更换中科大更新源
# 更换brew.git
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
更换核心软件仓库（homebrew-core.git)
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
更换Home cask软件仓库
cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask
如果未找到就不执行git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
更换Home Bottles源
bash终端:echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
zhs终端:echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zsh
重置默认源
重置brew.git
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
重置核心软件仓库
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git
重置Homebrew cask软件仓库
cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask
git remote set-url origin https://github.com/Homebrew/homebrew-cask
# 重置Homebrew Bottles源
# 注释掉终端配置文件里的有关Homebrew Bottles即可恢复官方源。 重启终端或重读配置文件
```

```shell
# mac 启动ssh服务
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist
sudo launchctl list | grep ssh
# 或者从设置里设置远程功能
```



```
vim -b file # 二进制格式打开
:%!xxd -g 1 # 切换显示 
:%!xxd -r 返回文本显示
```

```shell
ls -R # 递归
```

```shell
python3 -u test.py 2&>1 >> log
```





```
mac 忽略更新
1、屏蔽系统更新：sudo softwareupdate --ignore "macOS Catalina"
2、还原系统更新：sudo softwareupdate --reset-ignored
3、去除更新角标代码：defaults write com.apple.systempreferences AttentionPrefBundleIDs 0
```



