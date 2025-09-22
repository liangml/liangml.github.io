# Mysql

# 常用命令
## mysql导出
> --skip-extended-insert 跳过多行写入 --skip-quote-names 跳过 ` 表名 --complete-insert 带字段的insert
### 结构导出
```shell
mysqldump -uroot -pxxx \
--default-character-set=utf8 \
--set-gtid-purged=off \
--compact \
--no-data \
--databases xxx \
--tables xxx > $(date +%Y%m%d%H%M%S)_struct.sql
```
### 数据导出
```shell
mysqldump -uroot -pxxx \
--default-character-set=utf8 \
--set-gtid-purged=off \
--compact \
--no-create-info \
--skip-quote-names \
--complete-insert \
--databases xxx \
--tables xxx > $(date +%Y%m%d%H%M%S)_data.sql
```
### 数据加条件导出
```shell
mysqldump -uroot -pxxx \
--default-character-set=utf8 \
--set-gtid-purged=off \
--compact \
--no-create-info \
--skip-quote-names \
--complete-insert \
--databases xxx \
--tables xxx \
--where="sqlxxx"> $(date +%Y%m%d%H%M%S)_data.sql
```
### 整库备份
```shell
# 建表语句+数据
mysqldump -u USER -p -h HOST -B DATABASENAME --single-transaction --default-character-set=utf8 --set-gtid-purged=off > DATABASENAME_$(date +%Y%m%d%H%M%S).sql
```

## binlog日志查看
```plain
mysqlbinlog --base64-output=decode-rows -v -v mysql-bin.021530 >021530.sql
```

## 慢进程查看
```sql
select id,user,host,db,command,time,state,info from information_schema.PROCESSLIST order by time desc;
```

## Slave 链接 master 配置
```sql
CHANGE MASTER TO MASTER_HOST='IPADDRESS',MASTER_USER='UserName',MASTER_PASSWORD='PassWord',master_log_file='BinLogFile',master_log_pos=POSITION;
```

## 从库扩容备份操作
### 1. 备份到目标机器:
```shell
innobackupex --defaults-file=/etc/my.cnf --no-timestamp --user=root --password="PassWord" --compress --parallel=4 --compress-threads=4 --stream=xbstream /tmp/backup | ssh root@IPADDRESS "xbstream -x -C /DSTDIR"
```

### 2. 从库扩容操作:
* 目标机器解压
```shell
innobackupex --parallel=8 --decompress ./
```
* 目标机器初始化
```shell
innobackupex --use-memory=51200M --apply-log ./
```
* 目标机器恢复
```shell
innobackupex --defaults-file=/etc/my.cnf --copy-back ./2017-08-23_21-23-46/
```
* 清理备份文件
```shell
find /var/lib/mysql -name "*.qp" | xargs rm
chown -R mysql.mysql /var/lib/mysql
```

## mysql 从库繁忙配置：
```sql
innodb_flush_log_at_trx_commit = 2
sync_binlog=1
```

```sql
set global innodb_flush_log_at_trx_commit=0;
set global sync_binlog=0;
```

## mysql8.0密码更改原生：
```sql
set global validate_password.policy=0;
set global validate_password.length=1;
ALTER user 'root'@'localhost' IDENTIFIED BY 'PASSWORD';

ALTER USER 'UserName'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord';
```



## mysql slave 权限配置：
```sql
CREATE USER 'rpl'@'172.16.1.%' IDENTIFIED  BY 'xxx';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'rpl'@'172.16.1.%';
```

## information_schema
* 表空间优化
> 碎片大小 = 数据总大小 - 实际表空间文件大小 数据总大小 = Data_length + Index_length = 101842944<br>
> 实际表空间文件大小 = rows_Avg_row_length = 101177624<br>
> 碎片大小 = (101842944 - 101177624) / 1024 /1024 = 0.63MB

* 整理碎片
> alter table table_name engine = innodb<br>
> pt-online-schema-change optimize table<br>
> 命令整理: show table status from DBNAME like '%TABLENAME%' \G 查看;

* pt-online-schema-change-shell
```shell
#!/bin/bash
source /etc/profile
pt-online-schema-change \
--defaults-file=/etc/my.cnf \
-uroot -h localhost --password=PASSWORD \
--alter="ENGINE=InnoDB" \
D=DBNAME,t=TABLENAME \
1--no-check-replication-filters --alter-foreign-keys-method=auto \
1--recursion-method=none --print \
1--charset=utf8 --max-load="Threads_running=100" \
1--critical-load="Threads_running=200" --execute
```
* 查看所有数据库的容量
```sql
SELECT
    table_schema as '数据库',
    sum(table_rows) as '记录数',
    sum(truncate(data_length/1024/1024, 2)) as '数据容量(MB)',
    sum(truncate(index_length/1024/1024, 2)) as '索引容量(MB)',
    sum(truncate(DATA_FREE/1024/1024, 2)) as '碎片占用(MB)'
    from information_schema.tables
group by table_schema
order by sum(data_length) desc, sum(index_length) desc;
```
* 查看指定库的大小
```sql
SELECT
    table_schema as '数据库',
    sum(table_rows) as '记录数',
    sum(truncate(data_length/1024/1024, 2)) as '数据容量(MB)',
    sum(truncate(index_length/1024/1024, 2)) as '索引容量(MB)',
    sum(truncate(DATA_FREE/1024/1024, 2)) as '碎片占用(MB)'
    from information_schema.tables
where table_schema='DBNAME'
order by data_length desc, index_length desc;
```
* 查看指定库所有表的大小
```sql
SELECT
  table_schema as '数据库',
  table_name as '表名',
  table_rows as '记录数',
  truncate(data_length/1024/1024, 2) as '数据容量(MB)',
  truncate(index_length/1024/1024, 2) as '索引容量(MB)',
  truncate(DATA_FREE/1024/1024, 2) as '碎片占用(MB)'
from
  information_schema.tables
where
  table_schema='DBNAME'
order by
  data_length desc, index_length desc;
```
* 查看数据库中容量排名前十的表
```sql
USE information_schema;
SELECT
  TABLE_SCHEMA as '数据库',
  table_name as '表名',
  table_rows as '记录数',
  ENGINE as '存储引擎',
  truncate(data_length/1024/1024, 2) as '数据容量(MB)',
  truncate(index_length/1024/1024, 2) as '索引容量(MB)',
  truncate(DATA_FREE/1024/1024, 2) as '碎片占用(MB)'
from  tables
order by table_rows desc limit 10;
```
# 分区
* 重组
```sql
ALTER TABLE tableName REORGANIZE PARTITION pmax INTO(
  PARTITION partitionName VALUES LESS THAN (890000000),
  PARTITION pmax VALUES LESS THAN MAXVALUE
);
-- 语法分析: 重组对应分区表最大分区pmax,将结果放入新的分区 依次递增。
```

* 添加
```sql
-- 递增：1209600
ALTER TABLE history ADD PARTITION (PARTITION p20210820 VALUES LESS THAN (1637337600));
```

## mysql CSV入库
```shell
mysql -h IPADDRESS -uroot -pxxx DBNAME -e "load data local infile 'CSVFILE' into table $tableName FIELDS TERMINATED BY '\\\\t';"
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/mysql/  

