善用 find、grep 命令可以高效地查找内容、文件，这样有利于定位分析日志，定位问题和提高工作效率。所以下面就整理了这两个命令的作用和用法。

## 1、find命令
find命令用于：在一个目录（及子目录）中搜索文件，你可以指定一些匹配条件，如**按文件名**、**文件类型**、**用户**、**时间戳** 查找文件。

### 1.1、find命令的一般形式
man文档中给出的find命令的一般形式为：

```
find [-H] [-L] [-P] [-D debugopts] [-Olevel] [path...] [expression]
```

上面的find命令的常用形式可以简化为：

```
find [path...] [expression]
```

- path：find命令所查找的目录路径。例如用`.`来表示当前目录，用`/`来表示系统根目录
- expression：expression可以分为——`-options [-print -exec -ok ...]`
- -options，指定find命令的常用选项，下节详细介绍
- -print，find命令将匹配的文件输出到标准输出
- -exec，find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为`'command' {  } \;，注意{   }和\；`之间的空格

<!-- find ./ -size 0 -exec rm {} \; 删除文件大小为零的文件 （还可以以这样做：rm -i `find ./ -size 0`  或 find ./ -size 0 | xargs rm -f &）
为了用ls -l命令列出所匹配到的文件，可以把ls -l命令放在find命令的-exec选项中：find . -type f -exec ls -l {  } \;
在/logs目录中查找更改时间在5日以前的文件并删除它们：find /logs -type f -mtime +5 -exec rm {  } \;
-ok，和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
find . -name "*.conf"  -mtime +5 -ok rm {  } \; 在当前目录中查找所有文件名以.LOG结尾、更改时间在5日以上的文件，并删除它们，只不过在删除之前先给出提示
也有人这样总结find命令的结构： -->

### 1.2、find命令的常用选项及实例
- -name  
**按照文件名查找文件。**  
`find /dir -name filename` 在`/dir`目录及其子目录下面查找名字为filename的文件  
`find . -name "*.c"` 在当前目录及其子目录（用`.`表示）中查找任何扩展名为“c”的文件
- -perm  
**按照文件权限来查找文件。**  
`find . -perm 755 –print` 在当前目录下查找文件权限位为755的文件，即文件属主可以读、写、执行，其他用户可以读、执行的文件
- -prune  
使用这一选项可以使find命令不在当前指定的目录中查找，如果同时使用-depth选项，那么-prune将被find命令忽略。  
`find /apps -path "/apps/bin" -prune -o –print` 在`/apps`目录下查找文件，但不希望在/apps/bin目录下查找
`find /usr/sam -path "/usr/sam/dir1" -prune -o –print` 在`/usr/sam`目录下查找不在dir1子目录之内的所有文件
- -mtime -n +n  
**按照文件的更改时间来查找文件**， `-n`表示文件更改时间距现在n天以内，`+n`表示文件更改时间距现在n天以前。  
`find / -mtime -5 –print` 在系统根目录下查找更改时间在5日以内的文件  
`find /var/adm -mtime +3 –print` 在`/var/adm`目录下查找更改时间在3日以前的文件
- -newer file1 ! file2  
**查找更改时间比文件file1新但比文件file2旧的文件。**
- -type  
**查找某一类型的文件**，诸如：  
    - b - 块设备文件。
    - d - 目录。
    - c - 字符设备文件。
    - p - 管道文件。
    - l - 符号链接文件。
    - f - 普通文件。  
  `find /etc -type d –print` 在`/etc`目录下查找所有的目录  
  `find . ! -type d –print` 在当前目录下查找除目录以外的所有类型的文件  
  `find /etc -type l –print` 在/etc目录下查找所有的符号链接文件  
  `-size n：[c]` 查找文件长度为n块的文件，带有c时表示文件长度以字节计。  
  `find . -size +1000000c –print` 在当前目录下查找文件长度大于1 M字节的文件  
  `find /home/apache -size 100c –print` 在`/home/apache`目录下查找文件长度恰好为100字节的文件  
  `find . -size +10 –print` 在当前目录下查找长度超过10块的文件（一块等于512字节）  

- -depth  
**在查找文件时，首先查找当前目录中的文件，然后再在其子目录中查找。**  
`find / -name "CON.FILE" -depth –print` 它将首先匹配所有的文件然后再进入子目录中查找



### 1.3、find与xargs
在使用 find 命令的`-exec`选项处理匹配到的文件时，find 命令将所有匹配到的文件一起传递给 exec 执行。但有些系统对能够传递给 exec 的命令长度有限制，这样在 find 命令运行几分钟之后，就会出现溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是 xargs 命令的用处所在，特别是与 find 命令一起使用。

**find 命令把匹配到的文件传递给 xargs 命令，而xargs命令每次只获取一部分文件而不是全部，不像-exec选项那样。这样它可以先处理最先获取的一部分文件，然后是下一批，并如此继续下去。**

在有些系统中，使用`-exec`选项会为处理每一个匹配到的文件而发起一个相应的进程，并非将匹配到的文件全部作为参数一次执行；这样在有些情况下就会出现进程过多，系统性能下降的问题，因而效率不高；

而使用xargs命令则只有一个进程。另外，在使用xargs命令时，究竟是一次获取所有的参数，还是分批取得参数，以及每一次获取参数的数目都会根据该命令的选项及系统内核中相应的可调参数来确定。

来看看 xargs 命令是如何同 find 命令一起使用的，并给出一些例子。

- `find . -type f -print | xargs file` 查找系统中的每一个普通文件，然后使用xargs命令来测试它们分别属于哪类文件
- `find / -name "core" -print | xargs echo "" >/tmp/core.log` 在整个系统中查找内存信息转储文件(core dump) ，然后把结果保存到/tmp/core.log 文件中：
- `find . -type f -print | xargs grep "hostname"` 用 grep 命令在所有的普通文件中搜索 hostname 这个词

- `find ./ -mtime +3 -print|xargs rm -f –r` 删除3天以前的所有东西

- `find ./ -size 0 | xargs rm -f &` 删除文件大小为零的文件

find 命令配合使用`exec`和`xargs`可以使用户对所匹配到的文件执行几乎所有的命令。

## 2、grep命令
**grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。**

### 2.1、grep命令的一般选项及实例
```
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]
```
grep 命令用于搜索由 Pattern 参数指定的模式，并将每个匹配的行写入标准输出中。

### 2.2、grep正则表达式元字符集(基本集)
- `^`  锚定行的开始 如：'^grep'匹配所有以grep开头的行。

- `$`  锚定行的结束 如：'grep$'匹配所有以grep结尾的行。

- `.`   匹配一个非换行符的字符 如：'gr.p'匹配gr后接一个任意字符，然后是p。

- `*`  匹配零个或多个先前字符 如：' \*grep'匹配所有一个或多个空格后紧跟grep的行。 `.*`一起用代表任意字符。

- `[]` 匹配一个指定范围内的字符，如'[Gg]rep'匹配Grep和grep。

- `[^]`  匹配一个不在指定范围内的字符，如：'[^A-FH-Z]rep'匹配不包含A-F和H-Z的一个字母开头，紧跟rep的行。

- `\(..\)`  标记匹配字符，如：'\\(love\\)'，love被标记为1。

- `\<`  锚定单词的开始，如：'\\<grep'匹配包含以grep开头的单词的行。

- `\>`  锚定单词的结束，如'grep\\>'匹配包含以grep结尾的单词的行。

- `x\{m\}` 连续重复字符x，m次，如：'o\\{5\\}'匹配包含连续5个o的行。

- `x\{m,\}` 连续重复字符x,至少m次，如：'o\\{5,\\}'匹配至少连续有5个o的行。

- `x\{m,n\}` 连续重复字符x，至少m次，不多于n次，如：'o\\{5,10\\}'匹配连续5--10个o的行。

- `\w`  匹配一个文字和数字字符，也就是[A-Za-z0-9]，如：'G\\w*p'匹配以G后跟零个或多个文字或数字字符，然后是p。

- `\W`  w的反置形式，匹配一个非单词字符，如点号句号等。\\W*则可匹配多个。

- `\b`  单词锁定符，如: '\\bgrep\\b'只匹配grep，即只能是grep这个单词，两边均为空格。

### 2.3、grep命令的常用选项及实例
- -?  
**同时显示匹配行上下的 ？行**，如：`grep -2 pattern filename` 同时显示匹配行的上下2行。

- -b  
**打印匹配行前面打印该行所在的块号码。**

- -c,--count  
**只打印匹配的行数，不显示匹配的内容。**

- -f File，--file=File  
**从文件中提取模板。空文件中包含0个模板，所以什么都不匹配。**

- -h，--no-filename  
**当搜索多个文件时，不显示匹配文件名前缀。**

- -i，--ignore-case  
**忽略大小写差别。**

- -q，--quiet  
**取消显示，只返回退出状态。0则表示找到了匹配的行。**

- -l，--files-with-matches  
**打印匹配模板的文件清单。**

- -L，--files-without-match  
**打印不匹配模板的文件清单。**

- -n，--line-number  
**在匹配的行前面打印行号。**

- -s，--silent  
**不显示关于不存在或者无法读取文件的错误信息。**

- -v，--revert-match  
**反检索，只显示不匹配的行。**

## 常用命令组合
- `ls -l | grep '^a'` 通过管道过滤`ls -l`输出的内容，只显示以a开头的行。

- `grep 'test' d*` 显示所有以d开头的文件中包含test的行。

- `grep 'test' aa bb cc` 显示在aa，bb，cc文件中匹配test的行。

- `grep '[a-z]' aa` 显示所有包含每个字符串至少有5个连续小写字符的字符串的行。

- `grep -i pattern files` ：不区分大小写地搜索。默认情况区分大小写

- `grep -l pattern files` ：只列出匹配的文件名，

- `grep -L pattern files` ：列出不匹配的文件名，

- `grep -w pattern files` ：只匹配整个单词，而不是字符串的一部分(如匹配‘magic’，而不是‘magical’)，

- `grep -C number pattern files` ：匹配的上下文分别显示[number]行，

- `grep pattern1 | pattern2 files` ：显示匹配 pattern1 或 pattern2 的行，

- `grep pattern1 files | grep pattern2` ：显示既匹配 pattern1 又匹配 pattern2 的行。

## 参考
- [linux中强大且常用命令：find、grep](http://www.cnblogs.com/skynet/archive/2010/12/25/1916873.html)
