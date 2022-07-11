free 命令
==============================

## 1. 用途

展示系统内存使用情况

## 2. 常见使用方式

### 2.1 使输出更可读

- options: `-h/-b/-k/-m/-g`

- example:

```
$ free -h
             total       used       free     shared    buffers     cached
Mem:           33G        19G        14G       572K       198M       6.8G
-/+ buffers/cache:        12G        21G
Swap:           0B         0B         0B
```

### 2.2 持续显示实时内存使用状况

`free -s {second}`

- example:

```
$ free -s 5
```

### 2.3 持续显示实时内存使用状况数次

`free -c {count} -s {second}`

- example:

`free -c 4 -s 2`  //为KB为单位，每2秒显式系统内存使用情况，一共显示4次

## 3. Refs

http://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316438.html
