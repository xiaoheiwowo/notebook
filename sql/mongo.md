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


