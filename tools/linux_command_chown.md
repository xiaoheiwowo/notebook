### chown命令改变某个文件或目录的所有者和所属的组。

该命令可以向某个用户授权，使该用户变成指定文件的所有者或者改变文件所属的组。

用户可以是用户或者是用户id，用户组可以是组名或组id。文件名可以使由空格分开的文件列表，在文件名中可以包含通配符。 只有文件主和超级用户才可以便用该命令。

语法
```
chown (选项) (参数)
```

选项
- \-c或\-\-changes：效果类似“-v”参数，但仅回报更改的部分；
- \-f或\-\-quite或\-\-silent：不显示错误信息；
- \-h或\-\-no-dereference：只对符号连接的文件作修改，而不更改其他任何相关文件；
- \-R或\-\-recursive：递归处理，将指定目录下的所有文件及子目录一并处理；
- \-v或\-\-version：显示指令执行过程；
- \-\-dereference：效果和“-h”参数相同；
- \-\-help：在线帮助；
- \-\-reference=<参考文件或目录>：把指定文件或目录的拥有者与所属群组全部设成和参考文件或目录的拥有者与所属群组相同；
- \-\-version：显示版本信息。



### 命令举例
```
-rwxrw-r--  1 ubuntu adm       0 Mar 22 08:22 example
```
想改变文件所有者和group，即ubuntu、adm
实例：
- 改变文件owner为`another_user`：
```
chown another_user example
```
- 改变文件owner与所属group为`another_user`和`another_group`：
```
chown another_user:another_group example
```
- 递归地改变文件夹下所有文件、文件夹的所有者、group：
```
chown -R another_user:another_group example
```


参考资料: http://man.linuxde.net/chown
