#一，概述
    大致来说，是显示文件后面的内容，具体而言，就是从何位置开始显示到文件结束处的内容。
main tail
NAME
     tail -- display the last part of a file
SYNOPSIS
     tail [-F | -f | -r] [-q] [-b number | -c number | -n number] [file ...]

#二，参数分类
##1,位置参数：

 - -b number 从尾部倒数number个512-byte blocks.然后一直显示到尾部
 - -c number 从尾部倒数number个char，然后一直显示到尾部
 - -n number 从尾部倒数多少行。然后开始一直显示到尾部。
 其中 -5,5都表示从尾部算起。+5表示从文件头部算起到+5个单位(byte,block,line),然后从此位置一直显示到尾部。

##2,监视参数：
 
- -f 强制更新，每当文件写入进自动更新尾部内容。这是我们最常用的选项。
- -F 同上，就算是文件改名也能强制更新，常见于日志文件的会自动更名的情况。

##3,多文件参数

- -q 用来取消多个文件之间的分割符，一般情况下不建议使用。

#三, 例子：
##1， 显示多个文件最后一行，带q与不带q参数
```
> tail -n1 report-13*
==> report-1301 <==
Total tons output for month of January '13 was 523

==> report-1302 <==
Total tons output for month of February '13 was 272

==> report-1303 <==
Total tons output for month of March '13 was 623
```

```
> tail -q -n1 report-13*
Total tons output for month of January '13 was 523
Total tons output for month of February '13 was 272
Total tons output for month of March '13 was 623
```

##2 从文件第二行开始一直显示到尾部。
```
tail -n +2 report
```

##3 监视文件,是最常用的用法。在mac下，可以用command+k清屏。
```
tail -f log.log
```

#四，参考内容

- 1，man tail
- 2,https://en.wikipedia.org/wiki/Tail_(Unix)
- 3,https://zh.wikipedia.org/wiki/GNU核心工具组
- 4,https://github.com/coreutils/coreutils/blob/master/src/tail.c

