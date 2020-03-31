# MySQL

## 数据类型

- 整数：`tinyint`  `smallint`  `mediumint`  `int`  `bigint`，整形从左到右范围变大, **int 4字节**
- 小数：浮点 `float`  `double`   定点 **`decimal`**，存小数数据推荐使用decimal(m,d)  m总长 d小数长度，decimal(4,2) 0.00 ~ 99.99
- 日期：`year` `timestamp`  `time` `date` `datetime`

|日期时间类型  |占用空间    |日期格式    |最小值 |最大值 |零值表示 |
|-------------|:--------| ---------|---------|---------|--------|
|DATETIME |8 |bytes |YYYY-MM-DD HH:MM:SS |1000-01-01 00:00:00|9999-12-31 23:59:59 |
|**TIMESTAMP** |4 |bytes |YYYY-MM-DD HH:MM:SS |19700101080001| 2038 年的某个时刻|
|DATE |3 |bytes |YYYY-MM-DD |1000-01-01 |9999-12-31 |
|TIME |3 |bytes |HH:MM:SS |-838:59:59 |838:59:59 |
|YEAR |1 |bytes |YYYY |1901 |2155 |

也可以使用int存时间戳

- 字符串：set emum blob（二进制） **varchar** char  text
    - char(10) 定常字符串 不够用空格补齐，多了截取，存取速度快于varchar 0~255 字符
    - varchar(10) 变长 不够不补空格 节省空间最大65535   21844字符，长度设置65535会自动转换text 和编码有关
    - text 长文本 65535字节   tinytext **text** mediumtext longtext

- 字段名的修饰
    - unsigned  auto_increment defalut comment(字段解释说明)
    - not null / null 可以都使用not null
    - unique（唯一索引） 
    - index（普通索引）
    - primary key（主键）通常自增字段设为主键

- 运算符
    =赋值判断都用 != <> < > <= >= OR || AND && BETWEEN ... AND ...   IN NOT IN

```mysql
# 关于长度的说明
int(n) zerofill n 和最大存储长度无关 int 长度4字节
char(n) 存长度多余n的字符串会被截取前n个字符 字符 字符
length() char_length()函数  select length(name) from user;
varchar(n) 存长度多余n的字符串会被截取前n个字符 字符 字符
sql_mode 宽松模式 截取字符串只warning，严格模式字符串长会报错
```



## SQL语句

- DDL （defination 定义） 创建删除修改库表结构
- DML （manipulation 操作） 增删改表的记录
- DCL （control 控制） 用户的创建及授权
- DQL （query 查询） 查询数据

### DDL

```mysql
SHOW DATABASES; data目录下的文件夹
CREATE DATABASE shop;
DROP DATABASE shop;
USE shop;
CREATE TABLE user(
  	id int unsigned not null auto_increment  comment '用户id',
  	user_name varchar(20) not null conmment '用户名',
  	email varchar(50) not null comment  '',
  	age tinyint unsigned not null ,
  	fee decimal(10, 2) not null  default 0.00 ,
  	time timestamp not null ,
  	# 列名，列类型，其他关键词
  	primary key(id),
  	key `idx_user_name` (`user_name`),
    unique key `idx_email` (`email`),
    CONSTRAINT `user_ibfk_1` FOREIGN KEY (`room_id`) REFERENCES `meeting_room` (`object_id`),
	);
SHOW TABLES;
DESC user; 查看表结构
SHOW CREATE TABLE user;查看新建语句
DROP TABLE user;
ALTER TABLE user MODIFY user_name varchar(50); 改修饰
ALTER TABLE user CHANGE email user_email varchar(50) not null comment '';改字段名
ALTER TABLE user ADD password char(32) not null AFTER user_name;加字段
ALTER TABLE user DROP password;删字段
ALTER TABLE user RENAME (TO) users;改表名
```



### DML

```mysql
INSERT INTO user (user_name, password, email, fee, time) 
VALUES ('jack', password('123456'), 12.33, 'jack@125.com', time());
INSERT INTO VALUES();所有字段不能缺少

UPDATE user SET age=100 , ... WHERE user_name = 'jack';
DELETE FROM user WHERE id =1;
DELETE FROM user;删除所有数据id接以前的数据
TRUNCATE user; # 清空表,id从1开始

(SHOW VARIABLES LIKE '%char%';
SET NAMES gbk; 临时改编码
# 永久改配置文件 utf8 -> gbk)

```



### DCL

```shell
mysql -u{username} -p{password} -h{ip} -A

```



```mysql
# 使设置生效
flush privileges
# 创建用户
create user 'test'@'192.168.0.11' identified by '123456';
# 删除用户 
drop user user@ip

授权，删除授权grant (revoke) select insert ... (all) on 库名.*(*.*) to user@ip (identified by psw)
查看权限 show grants for user@ip
修改密码 mysql表，mysqladmin.exe
忘记密码跳过验证 mysqld.exe --skip-grant-tables
设定指定IP访问数据库 修改mysql表
[常用：mysql 表  user host authentication_string(password)字段]
```



### DQL

```mysql
select* from table；
查询结果去重 distinct 
连接字段 concat(field1, field) as new_name  concat_ws('-', field1, field) 
模糊查询 like '%xx%' (慢)--sphinx
排列order by   asc升序， desc降序
聚合函数count() AVG() COUNT() MAX() MIN() SUM()
分组查询group by 以某一字段分组 可以查询各组数量等
连接查询, where inner join where /on ///left join on /// right join on
联合查询union all 连接两条select语句，两个查询语句字段数量必须相同
子查询
SELECT newapply.object_id FROM newapply LEFT JOIN meeting_room ON newapply.room_id = meeting_room.object_id;
```



```mysql
# Json 
update templatebase set form_info = json_set(form_info,"$[2].title_first","会议主题") where types=3;
SELECT form_info->"$[2].title_first" from templatebase WHERE types = 3;
```



## 索引

```mysql
添加索引 alter table table_name add index/unique/fulltext index_name field_name
删除索引 alter table table_name drop index index_name
查看所有索引 show index from table_name
```

- 普通索引 key/index()
- 唯一索引 unique key()  不允许插入已有的数据
- 主键索引 primary key()
- 全文索引 fulltext()  
    select * from table_name where match(field_name) against(' ')
    中文查询需要分词
    like模糊查询耗时
    外键约束 for
    组合索引

>  索引的数据结构，B树，哈希

## 引擎

- MyISAM 全文索引 不支持事务 表级锁 崩溃恢复支持不好
- InnoDB 支持事务 全文索引 行级锁 性能好

```mysql
# 查看表状态 
show table status\G;
# 指定引擎
create table user(name char(10)) engine=innodb;
```



## 字符集




```mysql
# 指定字符集校对case insensitive
create table user(name char(10)) charset=utf8 collate=utf8_general_ci/cs
```



## 启停导入导出

```shell
# 启动mysql：
sudo /etc/init.d/mysql start 
sudo service mysql start

# 停止mysql：
sudo /etc/init.d/mysql stop 
sudo service mysql stop

# 重启mysql：
sudo/etc/init.d/mysql restart
sudo service mysql restart

# 数据库导入导出 mysqldump或者navicate phpmyadmin
mysqldump -uroot -p shop user >sql.sql
mysql -uroot -p shop < sql.sql
```



## 优化方法

- 慢查询
    log_slow_queries 记录慢查询
    long_query_time
    set global log_slow_queries = on 开启慢查询日志(会花费时间)
- 性能分析
    profiling
    set profiling= on;
    show profiles;
    show profile for query ID;
    
    ```sql
    // explain
    explain select * from table where id=1;
    ```
    
    
    
- 建索引加快查询

- 缓存

    cache 
    qcache_hits

    [mysqld]
    query_cache_size=1024000

- where 后字段加索引

- 不要select *

- limit 1

- 减少使用内置函数

- 减少在sql语句中运算

- like 左模糊查询不使用索引，扫全表

- 分区分表
- 读写分离
- 架构 负载均衡 高可用 易扩展



## 配置

```mysql
[mysqld]
skip-grant-tables # 忘记密码
character_set_server=utf8 # 字符集
```

- mysql库user表 root的host 改为 ’%‘ 可以使所有ip登录



## 数据表的设计原则
- 1NF 原子性 字段不可再分为多个字段 关系型数据库都满足
- 2NF 数据唯一区分，主键 id
- 3NF 有重复数据适合使用多张表描述 还可以使用外键



```sh
# 删除mysql:
sudo apt-get remove mysql-*
# 清理残留的数据
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```


```mysql
# CASE WHEN
UPDATE Personnel
SET salary =
CASE WHEN salary >= 5000 THEN salary * 0.9
     WHEN salary >= 2000 AND salary < 4600	THEN salary * 1.15
ELSE salary END; 
```



```
like '%aaa%'不会使用索引而like "aaa%"可以使用索引
索引不能含有null
索引最左匹配
```



```sql
# 设置主键自增
set @@auto_increment_offset = 1;     -- 起始值
set @@auto_increment_increment = 2;  -- 步长

# 禁用外键检查
SET @@foreign_key_checks=0;
# 启用外键检查
SET @@foreign_key_checks=1;


```

```mysql
## 重新索引k
alter table T drop index k; 
alter table T add index(k);
## 重建主键索引
alter table T drop primary key; 
alter table T add primary key(id);
上面的 重建主键的过程不合理。不论是删除主键还是创建主键，都会将整个表重建。所以连着执行这两个语句的话，第一个语句就白做了。这两个语句，可以用这个语句代替 ： alter table T engine=InnoDB;
```



## delete、drop、truncate区别

- truncate 和 delete只删除数据，不删除表结构 ,drop删除表结构，并且释放所占的空间。
- **删除数据的速度，**drop> truncate > delete
- delete属于DML语言，需要事务管理，commit之后才能生效。drop和truncate属于DDL语言，操作立刻生效，不可回滚。
- **使用场合：**
    - 当你不再需要该表时， 用 drop;
    - 当你仍要保留该表，但要删除所有记录时， 用 truncate;
    - 当你要删除部分记录时（always with a where clause), 用 delete.

 

**注意：** 对于**有主外键关系的表**，不能使用truncate而应该**使用不带where子句的delete语句**，由于truncate不记录在日志中，不能够激活触发器

 



##《MySQL技术内幕 - InnoDB存储引擎》

- 数据库和数据库实例的区别
    - 数据库：文件（磁盘上的，或内存中的）
    - 数据库实例：后台线程和一个共享内存区





非叶节点的指针指向子节点的页，先将整页读到内存在找到要的节点，页的大小决定树的容量，默认2000w左右，可以改页大小

用主键聚集索引B+树维护数据行的物理存储

辅助索引B+树叶节点存数据行的主键

