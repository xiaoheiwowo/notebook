## ngrep

```
ngrep
sudo ngrep -d any -q -Wbyline . port 8125
```



## statsd

```

```



## wrk

```
wrk -t12 -c200 -d30s --latency 'http://localhost:8044/users'
```

## netstat

```
# 查看 tcp 链接状态
netstat -tan |grep ^tcp |awk '{++a[$6]} END{for (i in a) print i, a[i]}'
```

## gitlib-ci

```
https://www.jianshu.com/p/d63d9941565f
http://www.imooc.com/article/262268
https://www.cnblogs.com/qulianqing/p/9156112.html
```



strace, ltrace



tcpdum

```
sudo tcpdump -i lo0 port 12121
```





### lsof

https://www.jianshu.com/p/a3aa6b01b2e1

