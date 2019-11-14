# shell

## if

- 基本语法

```shell
# if
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

```shell
# 目录判断
[ -b FILE ] # 如果 FILE 存在且是一个块特殊文件则为真。
[ -c FILE ] # 如果 FILE 存在且是一个字特殊文件则为真。
[ -d DIR ] # 如果 FILE 存在且是一个目录则为真。
[ -e FILE ] # 如果 FILE 存在则为真。
if [ -f FILE ] # 如果 FILE 存在且是一个普通文件则为真。
[ -g FILE ] # 如果 FILE 存在且已经设置了SGID则为真。
[ -k FILE ] # 如果 FILE 存在且已经设置了粘制位则为真。
[ -p FILE ] # 如果 FILE 存在且是一个名字管道(F如果O)则为真。
[ -r FILE ] # 如果 FILE 存在且是可读的则为真。
[ -s FILE ] # 如果 FILE 存在且大小不为0则为真。
[ -t FD ] # 如果文件描述符 FD 打开且指向一个终端则为真。
[ -u FILE ] # 如果 FILE 存在且设置了SUID (set user ID)则为真。
[ -w FILE ] # 如果 FILE存在且是可写的则为真。
[ -x FILE ] # 如果 FILE 存在且是可执行的则为真。
[ -O FILE ] # 如果 FILE 存在且属有效用户ID则为真。
[ -G FILE ] # 如果 FILE 存在且属有效用户组则为真。
[ -L FILE ] # 如果 FILE 存在且是一个符号连接则为真。
[ -N FILE ] # 如果 FILE 存在 and has been mod如果ied since it was last read则为真。
[ -S FILE ] # 如果 FILE 存在且是一个套接字则为真。
[ FILE1 -nt FILE2 ] # 如果 FILE1 has been changed more recently than FILE2, or 如果 FILE1 exists and FILE2 does not则为真。
[ FILE1 -ot FILE2 ] # 如果 FILE1 比 FILE2 要老, 或者 FILE2 存在且 FILE1 不存在则为真。
[ FILE1 -ef FILE2 ] # 如果 FILE1 和 FILE2 指向相同的设备和节点号则为真。
```

```shell
# 字符串判断
[ -z STRING ] 如果STRING的长度为零则为真 ，即判断是否为空，空即是真；
[ -n STRING ] 如果STRING的长度非零则为真 ，即判断是否为非空，非空即是真；
[ STRING1 = STRING2 ] 如果两个字符串相同则为真 ；
[ STRING1 != STRING2 ] 如果字符串不相同则为真 ；
[ STRING1 ]　 如果字符串不为空则为真,与-n类似
```

```shell
# 数值判断
INT1 -eq INT2           INT1和INT2两数相等为真 ,=
INT1 -ne INT2           INT1和INT2两数不等为真 ,<>
INT1 -gt INT2            INT1大于INT1为真 ,>
INT1 -ge INT2           INT1大于等于INT2为真,>=
INT1 -lt INT2             INT1小于INT2为真 ,<</div>
INT1 -le INT2             INT1小于等于INT2为真,<=
```

```shell
# 逻辑判断
-a / && 与
-o / || 或
! 非

# exp1: 如果a>b且a
if (( a > b )) && (( a < c ))
if [[ $a > $b ]] && [[ $a < $c ]]
if [ $a -gt $b -a $a -lt $c ]
if [ a>b && a<c ]

# exp2:如果a>b或a
if (( a > b )) || (( a < c ))
if [[ $a > $b ]] || [[ $a < $c ]]
if [ $a -gt $b -o $a -lt $c ]
```

```shell
# [[ ]] / [] / test

```



