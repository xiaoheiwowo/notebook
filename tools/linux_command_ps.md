## 说明

Linux中的ps命令是Process Status的缩写。ps命令用来列出系统中当前运行的那些进程。

ps工具标识进程的5种状态码: 
D 不可中断 uninterruptible sleep (usually IO) 
R 运行 runnable (on run queue) 
S 中断 sleeping 
T 停止 traced or stopped 
Z 僵死 a defunct (”zombie”) process 

## 使用方法

    ps [参数]

命令参数：
a  显示所有进程
-a 显示同一终端下的所有程序
-A 显示所有进程
c  显示进程的真实名称
-N 反向选择
-e 等于“-A”
e  显示环境变量
f  显示程序间的关系
-H 显示树状结构
r  显示当前终端的进程
T  显示当前终端的所有程序
u  指定用户的所有进程
-au 显示较详细的资讯
-aux 显示所有包含其他使用者的行程 
-C<命令> 列出指定命令的状况
--lines<行数> 每页显示的行数
--width<字符数> 每页显示的字符数
--help 显示帮助信息
--version 显示版本显示

### 例1

显示所有进程信息

    #ps -A  
    PID TTY          TIME       CMD
    1       ?              00:00:00 init
    2       ?              00:00:01 migration/0
    ...
  
### 例2

显示所有进程信息，连同命令行

    #ps -ef
    UID        PID  PPID  C STIME TTY   TIME       CMD
    root         1     0         0 Apr10 ?        00:00:00 init [3]                  
    root         2     1         0 Apr10 ?        00:00:01 [migration/0]
    root         3     1         0 Apr10 ?        00:00:00 [ksoftirqd/0]
    ...
    #ps -ef|grep ssh
    root       2720           1   0    Nov02      ?        00:00:00 /usr/sbin/sshd
    root     17394   2720   0    14:58        ?        00:00:00 sshd: root@pts/0 
    root     17465 17398  0    15:57 pts/0       00:00:00 grep ssh

各相关信息的意义：
UID 程序被该 UID 所拥有
PID 就是这个程序的 ID ！
PPID 则是其上级父程序的ID
C CPU 使用的资源百分比
TTY 登入者的终端机位置
TIME 使用掉的 CPU 时间。
CMD 所下达的指令为何

### 例3

列出目前所有的正在内存当中的程序

    #ps aux
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root         1       0.0         0.0         10368   676 ?        Ss     Apr10   0:00  init [3]                  
    root         2       0.0         0.0                   0        0 ?        S<    Apr10   0:01 [migration/0]
    root         3       0.0         0.0                   0        0 ?        SN   Apr10   0:00 [ksoftirqd/0]
    ...

USER：该 process 属于那个使用者账号的
PID ：该 process 的号码
%CPU：该 process 使用掉的 CPU 资源百分比
%MEM：该 process 所占用的物理内存百分比
VSZ ：该 process 使用掉的虚拟内存量 (Kbytes)
RSS ：该 process 占用的固定的内存量 (Kbytes)
TTY ：该 process 是在那个终端机上面运作，若与终端机无关，则显示 ?，另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。
STAT：该程序目前的状态，主要的状态有
R ：该程序目前正在运作，或者是可被运作
S ：该程序目前正在睡眠当中 (可说是 idle 状态)，但可被某些讯号 (signal) 唤醒。
T ：该程序目前正在侦测或者是停止了
Z ：该程序应该已经终止，但是其父程序却无法正常的终止他，造成 zombie (疆尸) 程序的状态
START：该 process 被触发启动的时间
TIME ：该 process 实际使用 CPU 运作的时间
COMMAND：该程序的实际指令

### 例4

找出与 cron 与 syslog 这两个服务有关的 PID 号码

    #ps aux|egrep '(cron|syslog)'
    syslog         786  0.1  0.0 434464   812         ?        Ssl  Apr10  1:44 rsyslogd
    root             981  0.0  0.0    23652     68         ?        Ss   Apr10   0:00 cron
    ubuntu    5660  0.0  0.0      8164   912 pts/1        S+   06:13   0:00 egrep (cron|syslog)