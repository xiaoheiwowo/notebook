top 命令
==============================

## 1. 用途

实时显示系统运行的进程的详细信息

## 2. 常见使用方式

### 2.1 基本

- example:

```
$ top
top - 02:52:05 up 1 day, 48 min,  2 users,  load average: 0.20, 0.27, 0.24
Tasks: 127 total,   1 running, 126 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.5 us,  0.3 sy,  0.0 ni, 98.0 id,  0.1 wa,  0.0 hi,  0.0 si,  0.1 st
KiB Mem:  35122512 total, 20098464 used, 15024048 free,   204484 buffers
KiB Swap:        0 total,        0 used,        0 free.  7085016 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 2628 ubuntu    20   0  326176  50588   4264 S   6.0  0.1  80:50.74 python
 1302 root      20   0   60924  12644   1920 S   2.0  0.0  30:04.21 supervisord
  873 syslog    20   0  794808 520800    988 S   1.3  1.5  16:34.24 rsyslogd
 2633 ubuntu    20   0  208356  42704   6084 S   1.0  0.1   7:22.06 python
 1209 rethink+  20   0  633132  85252   5784 S   0.7  0.2  14:25.70 rethinkdb
 1512 ubuntu    20   0  251320  48236   8240 S   0.7  0.1   6:01.34 gunicorn
 1522 ubuntu    20   0  249900  45212   6808 S   0.7  0.1   5:46.83 gunicorn
 1525 ubuntu    20   0  253548  50584   8280 S   0.7  0.1   6:21.69 gunicorn
 1526 ubuntu    20   0  324740  46192   6808 S   0.7  0.1   5:41.01 gunicorn
```
### 2.2 不显示僵死进程

`top -i`

### 2.3 只显示某一用户的进程

`top -u {user_name}`

- example:

`top -u ubuntu`

### 2.4 只显示某一进程

`top -p $(pgrep -d ',' {process_name})`

- example:

`top -p $(pgrep -d ',' python)`

### 2.5 排序

P 按CPU使用排序

M 按MEM使用排序

## 3. 相似应用

### 3.1 `htop`

## 4. Refs

http://alvinalexander.com/linux/unix-linux-top-command-cpu-memory
