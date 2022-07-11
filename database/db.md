# 数据库选型报告

## 需求场景
无论是Bytepower还是Appserver都需要定下来使用何种数据库来进行数据存储。
具体的要求有：
1. 支持事务，以便处理业务逻辑
2. 功能尽可能全，因为无法预知后面的需求会有哪些新的要求，尤其对于bytepower server来讲，后面很有可能有数据上的各种分析需求等
3. 性能足够高，如果业务发展，后续单库数据量可能达到千万级甚至亿级，每次查询期望能在毫秒时间内返回结果
4. 配套工具足够多，基本的ORM期望有现成的，后续还可能会有读写分离、分库分表等功能需求。
## 同类对比（选型依据）
### 数据库分类
[分类来自DB-Engine官网](https://db-engines.com/en/ranking)
数据库的种类很多，划分标准也很多，常见的会分为关系型数据库和非关系型数据库两种。
+ 关系型（SQL）数据库：典型的如Mysql、Postgresql、Sql-server等
+ 非关系型（No-SQL）数据库：No-SQL有时候也被翻译为Not Only SQL，除了关系型数据库外，其他的都可以归为是No-SQL数据库，这些数据库也可以按照其他维度进一步划分：
	+ 文档型数据库：MongoDB、DynamoDB、CouchBase等
	+ 时序型数据库：InfluxDB、Prometheus等
	+ 搜索引擎：ElasticSearch、Solr等
	+ Key-Value 数据库：Redis、etcd、Memcached等
	+ 图数据库：Neo4j等
	+ 列式存储数据库：Hbase、Cassandra等

### MySQL VS Postgresql 
Mysql 是最流行的数据库，并不是完全遵守 SQL 标准，对于连接使用的是多线程模式，消耗资源更少，之前开源后被收购。
Postgresql 是最先进的数据库，旨在严格遵守 SQL 标准，功能齐全，数据结构多样，对于复杂查询等表现优异，完全开源，社区活跃。

具体的各自优缺点可参考[对比文章A](https://www.biaodianfu.com/mysql-vs-postgresql.html)和[对比文章B](https://www.xplenty.com/blog/postgresql-vs-mysql-which-one-is-better-for-your-use-case/)
两者在设计上的不同之处可以参考下图：[图片来源](https://medium.com/hackernoon/showdown-mysql-8-vs-postgresql-10-3fe23be5c19e)（这个作者推崇Mysql，我们仅看其不同之处）
![](https://bp-doc.atcloudbox.com/uploads/upload_ac16e659ab01ba7e82320ce44b04b5cf.png)


### 最终选型
我们的场景需要的是关系型数据库，在MySQL与Postgresql之间，我们最终选择了Postgresql，具体理由如下：
1. Postgresql数据结构丰富，功能全，性能好，支持比较完整的SQL标准，对于未来潜在的业务需求（如可能的数据迁移、OLAP分析等），postgresql的支持更好；
2. 我们采用AWS提供的[Aurora的postgresql版本](https://aws.amazon.com/cn/rds/aurora/postgresql-features/)，其性能更好，同时也能节省我们的运维成本；
3. 从整体趋势上来讲，在国外使用postgresql的越来越多，绝大部分公司即使自己研发也是基于postgresql的多一些

使用Postgresql的缺点如下：
1. 对开发人员能力要求更高，一般情况下尤其是appserver，业务的用法和强度其实不大，大部分研发也没有时间精力深入学习研究Postgresql，很多特性其实一时半会用不上
2. Postgresql其实无法保证长时间稳定可用，需要[定时运行VACUUM](http://www.postgres.cn/news/viewone/1/390)，具体参数配置等需要调整，且如果其运行时机和业务高峰赶到一起，可能引发数据库服务不稳定。
3. 我们使用AWS提供的[Aurora的postgresql版本](https://aws.amazon.com/cn/rds/aurora/postgresql-features/)，其是在开源postgresql的基础上做了封装修改，但在简化我们运维成本的同时，也让我们的处理分析能力受限，很多工具命令等我们无法使用，且数据库版本等也受限于AWS提供的能力。


## 参考文档

[Postgresql 官网](https://www.postgresql.org/)
[Mysql 官网](https://www.mysql.com/cn/)
[MySQL与PostgreSQL哪个更好？](https://www.biaodianfu.com/mysql-vs-postgresql.html)
[MySQL vs PostgreSQL](https://www.xplenty.com/blog/postgresql-vs-mysql-which-one-is-better-for-your-use-case/)
[如何选择合适的数据库](https://juejin.cn/post/6844904057925009415)
[AWS 如何选择合适的数据库](https://aws.amazon.com/cn/startups/start-building/how-to-choose-a-database/)