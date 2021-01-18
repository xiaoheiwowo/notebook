##  MongoDB

```
collection -> table
document -> row
```

```sh

```

```sh
# 连接数据库URI
mongodb://user_name:pass_word@host:port/db
```



- 数据类型

| 数据类型           | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                            |
| Double             | 双精度浮点值。用于存储浮点值。                               |
| Min/Max keys       | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Array              | 用于将数组或列表或多个值存储为一个键。                       |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                       |
| Object             | 用于内嵌文档。                                               |
| Null               | 用于创建空值。                                               |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的 ID。                                 |
| Binary Data        | 二进制数据。用于存储二进制数据。                             |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                 |
| Regular expression | 正则表达式类型。用于存储正则表达式。                         |



```sh
# ObjectId 主键
_id(12bytes) = timestamp(4) + client_id(3) + pid(2) + counter(3)
```



```js

show dbs
use db
db
db.table_name.insert({})
db.createCollection("user")
db.user.drop()
show tables
db.dropDatabase()
db.table_name.insertMany([{},{}])
db.table_name.find().limit(2)
db.inventory.find( {query_document}, { item: 0, status: 1 } ); // 0排除，1显示
# INSERT
insertOne, insertMany, updateOne, updateMany, replaceOne, deleteOne, deleteMany, findOneAndUpdate, save, bulkWrite
# QUERY
db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
=> SELECT * FROM inventory WHERE status = "A" OR qty < 30

db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
=> SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
db.inventory.find( { "size.h": { $lt: 15 }, "size.uom": "in", status: "D" } )

```

## mongodb远程连接配置

1.修改mongodb的配置文件，让其监听所有外网ip

命令：`vim /etc/mongodb.conf`

```
bind_ip=127.0.0.1，这一行注释掉或者是修改成 bind_ip = 0.0.0.0
port = 27017，这一行为监听端口
auth=False，这一行为登陆验证
```

2.重启mongodb服务

命令：`/etc/init.d/mongodb restart`

2.1防火墙开放27017端口

命令：`iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 27017 -j ACCEPT`

 

3.远程连接

要连接的IP：`134.567.345.23`

命令：`mongo 134.567.345.23:27017`

这样就可以连接到134.567.345.23的mongodb/test的数据库

 

### ××补充：连接到自定义的用户××

1.增加

```
> use admin
switched to db admin
> db.addUser('username','password') 
```

2.远程连接

命令：` mongo 134.567.345.23:27017/admin -uusername -p`

输入password即可

3、删除用户

`db.removeUser('username')`

