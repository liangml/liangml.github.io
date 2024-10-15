# Postgres

# 导出
```shell
pg_dump -h IPADRESS  -U USERNAME -t TABLENAME  --column-inserts DATABASENAME &gt; BACKUPNAME.sql
```
# 表owner批量授权
```postgresql
REASSIGN OWNEDBY old_role [,...] TO new_role
```
# 主从配置
1. 主节点配置
```shell
# 安装postgre
# 切换postgres
su - postgres
# 登录
psql
# 管理员用户配置密码
ALTER USER postgres WITH PASSWORD &#39;YourPassWord&#39;;
# 创建备份账号及权限
CREATE ROLE replica login replication encrypted password &#39;replica&#39;;
# 验证账号是否成功
SELECT usename from pg_user;
# 验证权限
SELECT rolname from pg_roles;
# 编辑pg_hba.conf,设置replica用户白名单
vim /var/lib/pgsql/9.6/data/pg_hba.conf
host    all             all             &lt;从节点的VPC IPv4网段&gt;          md5     #允许VPC网段中md5密码认证连接
host    replication     replica         &lt;从节点的VPC IPv4网段&gt;          md5     #允许用户从replication数据库进行数据同步
# 编辑postgresql.conf
vim /var/lib/pgsql/9.6/data/postgresql.conf
# 分别找到以下参数
listen_addresses = &#39;*&#39;   #监听的IP地址
wal_level = hot_standby  #启用热备模式
synchronous_commit = on  #开启同步复制
max_wal_senders = 32     #同步最大的进程数量
wal_sender_timeout = 60s #流复制主机发送数据的超时时间
max_connections = 100    #最大连接数，从库的max_connections必须要大于主库的
# 重启服务
systemctl restart postgresql-9.6.service
```
2. 从节点配置
```shell
# 运行以下命令使用pg_basebackup基础备份工具指定备份目录。
pg_basebackup -D /var/lib/pgsql/9.6/data -h &lt;主节点IP&gt; -p 5432 -U replica -X stream -P
# 依次运行以下命令新建并修改recovery.conf配置文件。
cp /usr/pgsql-9.6/share/recovery.conf.sample /var/lib/pgsql/9.6/data/recovery.conf
vim /var/lib/pgsql/9.6/data/recovery.conf
# 分别找到以下参数，并将参数修改为以下内容：
standby_mode = on     #声明此节点为从库
primary_conninfo = ‘host=&lt;主节点IP&gt; port=5432 user=replica password=replica’ #对应主库的连接信息
recovery_target_timeline = ‘latest’ #流复制同步到最新的数据
# 运行以下命令打开postgresql.conf文件。
vim /var/lib/pgsql/9.6/data/postgresql.conf
max_connections = 1000             # 最大连接数，从节点需设置比主节点大
hot_standby = on                   # 开启热备
max_standby_streaming_delay = 30s  # 数据流备份的最大延迟时间
wal_receiver_status_interval = 1s  # 从节点向主节点报告自身状态的最长间隔时间
hot_standby_feedback = on          # 如果有错误的数据复制向主进行反馈
# 运行以下命令修改数据目录的属组和属主
chown -R postgres.postgres /var/lib/pgsql/9.6/data
```
3. 检测验证
```shell
# 检测验证需要主从节点之间存在数据交互，例如，从节点备份目录时，进行检测能够得到预期的结果。
pg_basebackup -D /var/lib/pgsql/96/data -h &lt;主节点IP&gt; -p 5432 -U replica -X stream -P
# 在主节点中运行以下命令查看sender进程。
ps aux |grep sender
# 在从节点中运行以下命令查看receiver进程。
ps aux |grep receiver
# 在主节点中进入PostgreSQL交互终端，输入以下SQL语句，在主库中查看从库状态。
select * from pg_stat_replication;
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/postgres/  

