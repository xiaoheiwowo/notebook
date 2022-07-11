## 1、grep命令
**grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。**

### 1.1、grep命令的一般选项及实例
```
grep [OPTIONS] PATTERN [FILE...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]
```
grep 命令用于搜索由 Pattern 参数指定的模式，并将每个匹配的行写入标准输出中。

### 1.2、grep正则表达式元字符集(基本集)
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

### 1.3、grep命令的常用选项及实例
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

## 2、常用命令组合
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

## 3、参考
- [linux中强大且常用命令：find、grep](http://www.cnblogs.com/skynet/archive/2010/12/25/1916873.html)
