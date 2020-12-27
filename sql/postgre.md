```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
ALTER TABLE table_name ALTER COLUMN id SET DEFAULT uuid_generate_v4();

```



```sql
pg_dump -U postgres -d dbname -t table_name > ./test.sql
psql -d newdatabase -U postgres -f test.sql 

pgcli -h localhost -p 5432 -d app_mars -U root -W password
```

```sql
alter table 表名 rename to 新表名
alter table 表名 rename 字段名 to 新字段名
```

```
nohup /usr/local/node/bin/node /www/im/chat.js >> /usr/local/node/output.log 2>&1 &
```

```
-- copy the table contents
COPY mytable TO '/tmp/mytable.dmp';
COPY mytable_restored FROM '/tmp/mytable.dmp';
```

```
nohup sudo -u postgres psql -h 
nohup sudo pg_dump -Fc -a -h host -d dbname -U username -t tablename > xxx.dump

# 并行导出
sudo pg_dump -Fd -j20 -a
nohup sudo pg_restore -Fc -a -h 
```

```
pg_column_size(any)	int	存储一个指定的数值需要的字节数（可能压缩过）
pg_database_size(oid)	bigint	指定OID的数据库使用的磁盘空间
pg_database_size(name)	bigint	指定名称的数据库使用的磁盘空间
pg_indexes_size(regclass)	bigint	关联指定表OID或表名的表索引的使用总磁盘空间
pg_relation_size(relation regclass, fork text)	bigint	指定OID或名的表或索引，通过指定fork('main', 'fsm' 或'vm')所使用的磁盘空间
pg_relation_size(relation regclass)	bigint	pg_relation_size(..., 'main')的缩写
pg_size_pretty(bigint)	text	Converts a size in bytes expressed as a 64-bit integer into a human-readable format with size units
pg_size_pretty(numeric)	text	把以字节计算的数值转换成一个人类易读的尺寸单位
pg_table_size(regclass)	bigint	指定表OID或表名的表使用的磁盘空间，除去索引（但是包含TOAST，自由空间映射和可视映射）
pg_tablespace_size(oid)	bigint	指定OID的表空间使用的磁盘空间
pg_tablespace_size(name)	bigint	指定名称的表空间使用的磁盘空间
pg_total_relation_size(regclass)	bigint	指定表OID或表名使用的总磁盘空间，包括所有索引和TOAST数据
```

```sql
select name, score, course, rank() over(partition by course order by score desc) as rank from jinbo.student;
```

```sql
select floor(extract(epoch from now()))||array_to_string(ARRAY(SELECT chr((65 + round(random() * 25)) :: integer) FROM generate_series(1,4)), '');

create function f_random_str(length INTEGER) returns character varying
    LANGUAGE plpgsql
AS $$
DECLARE
    result varchar(50);
BEGIN
    SELECT array_to_string(ARRAY(SELECT chr((65 + round(random() * 25)) :: integer)
                                 FROM generate_series(1,length)), '') INTO result;
    return result;
END
$$;

select f_random_str(4);

SELECT array_to_string(ARRAY(SELECT chr((65 + round(random() * 25)) :: integer) FROM generate_series(1,4)), '');

```

```sql
-- 查看 vacuum 状态
SELECT schemaname, relname, n_live_tup, n_dead_tup, last_autovacuum
FROM pg_stat_all_tables
ORDER BY n_dead_tup
    / (n_live_tup
       * current_setting('autovacuum_vacuum_scale_factor')::float8
          + current_setting('autovacuum_vacuum_threshold')::float8)
     DESC
LIMIT 10;

```

```
psql -h host -U user -d db -c 'select * from table limit 10' -o h.csv -A -F ","
```

```
select pg_terminate_backend(a.pid) from (select pid from pg_stat_activity where datname = 'app_mars' and app
 lication_name!='pgcli') a;
```

```
SELECT * FROM pg_locks pl LEFT JOIN pg_stat_activity psa
    ON pl.pid = psa.pid;
```

