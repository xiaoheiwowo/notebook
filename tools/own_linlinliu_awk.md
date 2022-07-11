## 说明

awk是一个强大的文本分析工具。简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。

## 使用方法

    awk '{pattern + action}' {filenames}

尽管操作可能会很复杂，但语法总是这样，其中 pattern 表示 AWK 在数据中查找的内容，而 action 是在找到匹配内容时所执行的一系列命令。

### 例1

显示/etc/passwd的账户

    #cat /etc/passwd |awk  -F ':'  '{print $1}'    
    root
    ubuntu
    
默认域分隔符是"空白键" 或 "[tab]键"，这里-F指定域分隔符为':'。$0则表示所有域,$1表示第一个域,$n表示第n个域。

### 例2

统计/etc/passwd:文件名，每行的行号，每行的列数，对应的完整行内容

    #awk  -F ':'  '{print "filename:" FILENAME ",linenumber:" NR ",columns:" NF ",linecontent:"$0}' /etc/passwd    //print示例
    #awk  -F ':'  '{printf("filename:%10s,linenumber:%s,columns:%s,linecontent:%s\n",FILENAME,NR,NF,$0)}' /etc/passwd  //printf示例
    filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
    filename:/etc/passwd,linenumber:2,columns:7,linecontent:daemon:x:1:1:daemon:/usr/sbin:/bin/sh
    filename:/etc/passwd,linenumber:3,columns:7,linecontent:bin:x:2:2:bin:/bin:/bin/sh
    filename:/etc/passwd,linenumber:4,columns:7,linecontent:sys:x:3:3:sys:/dev:/bin/sh

FILENAME，NR，NF，$?是awk的内置变量。
awk中同时提供了print和printf两种打印输出的函数。
其中print函数的参数可以是变量、数值或者字符串。字符串必须用双引号引用，参数用逗号分隔。如果没有逗号，参数就串联在一起而无法区分。这里，逗号的作用与输出文件的分隔符的作用是一样的，只是后者是空格而已。printf函数，其用法和c语言中printf基本相似,可以格式化字符串,输出复杂时，printf更加好用，代码更易懂。

## awk编程

awk中的条件语句，循环语句借鉴于C语言，支持while、do/while、for、break、continue，这些关键字的语义和C语言中的语义完全相同。
awk中的数组的下标可以是数字和字母，数组的下标通常被称为关键字(key)。值和关键字都存储在内部的一张针对key/value应用hash的表格里。由于hash不是顺序存储，因此在显示数组内容时会发现，它们并不是按照你预料的顺序显示出来的。数组和变量一样，都是在使用时自动创建的，awk也同样会自动判断其存储的是数字还是字符串。一般而言，awk中的数组用来从记录中收集信息，可以用于计算总和、统计单词以及跟踪模板被匹配的次数等等。

### 示例1

统计/etc/passwd的账户人数

    #awk '{count++;} END{print "user count is ", count}' /etc/passwd
    user count is 30
    #awk 'BEGIN {count=0;print "[start]user count is ", count} {count=count+1;} END{print "[end]user count is ", count}' /etc/passwd
    [start]user count is  0
    [end]user count is  30


count是自定义变量。之前的action{}里都是只有一个print,其实print只是一个语句，而action{}可以有多个语句，以;号隔开。

### 示例2

显示/etc/passwd的账户

    #awk -F ':' 'BEGIN {count=0;} {name[count] = $1;count++;}; END{for (i = 0; i < NR; i++) print i, name[i]}' /etc/passwd   //数组，for示例
    0 root
    1 ubuntu
    ...

## 配合zgrep，grep，cat，sed，tail，uniq，sort等命令使用

### 示例1

统计出20170301日的ialikes项目中提交任务最多的前20个用户以及任务数

    zgrep '"METRIC", "submit_task"' ialikes/20170301/*.gz |awk -F '"mid": "' '{print $2}' |awk -F '"' '{print $1}'  |sort |uniq -c |sort -n |tail -n 20 |sort -r

日志示例：
ialikes/20170301/ialikes_metric-1-2017-03-01-00-10-40-b45c29e7-decc-4fab-9505-9ceb4bd434d9.gz:["2017-03-01T00:10:41.667", "ip-192-168-13-30", "INFO", "ialikes.getlikes", "METRIC", "submit_task", {"queue": "medias:delivery:0", "publish_time": 1488327041, "desire_deliveries": 40, "uid": "3624329323", "desire_likes": 27, "app_id": 208, "mid": "643362", "likes_in_queue": 27, "platform": "ios", "usermeta": "{\"username\": null, \"fullname\": null, \"uid\": \"3624329323\"}", "media_id": "1459226670360014911_4710978647", "quantity": 25, "queue_score": 1488327041656, "media_url": "{\"low\": \"https://scontent-ord1-1.cdninstagram.com/t51.2885-15/s320x320/e35/16789800_1050357495108024_6067143808183697408_n.jpg?ig_cache_key=MTQ1OTIyNjY3MDM2MDAxNDkxMQ%3D%3D.2\", \"standard\": \"https://scontent-ord1-1.cdninstagram.com/t51.2885-15/e35/16789800_1050357495108024_6067143808183697408_n.jpg?ig_cache_key=MTQ1OTIyNjY3MDM2MDAxNDkxMQ%3D%3D.2\", \"thumbnail\": \"https://scontent-ord1-1.cdninstagram.com/t51.2885-15/s150x150/e35/16789800_1050357495108024_6067143808183697408_n.jpg?ig_cache_key=MTQ1OTIyNjY3MDM2MDAxNDkxMQ%3D%3D.2\"}"}]