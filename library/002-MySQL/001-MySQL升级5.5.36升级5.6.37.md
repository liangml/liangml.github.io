# MySQL源码5.5.36 =>> 5.6.37升级
升级的方法一般有两类：

1.利用mysqldump来直接导出sql文件，导入到新库中，这种方法是最省事儿的，也是最保险的，缺点的话，也显而易见，大库的mysqldump费时费力。

2.直接替换掉mysql的安装目录和my.cnf，利用mysql_upgrade 来完成系统表的升级，这种方法需要备份原有的文件，但属于物理拷贝，速度较快。缺点的话，跨版本升级不推荐这么做，比如mysql5.1升级到mysql5.6,mysql5.5升级到mysql5.7等。

本文采用的是第二种方法升级。

MySQL当前版本
```
[root@localhost ~]# /usr/local/mysql/bin/mysql -V
/usr/local/tdoa/mysql/bin/mysql  Ver 14.14 Distrib 5.5.36, for Linux (x86_64) using readline 5.1
```
备份my.cnf、MySQL安装目录、MySQL数据目录
```
[root@localhost ~]# cp -p /etc/init.d/mysql /etc/init.d/mysql.old
[root@localhost ~]# cp -p /etc/my.cnf /etc/my.cnf.old
[root@localhost ~]# cp -rp /usr/local/mysql /usr/local/mysql.old
[root@localhost ~]# rm -rf /usr/local/mysql
```
安装最新版本的MySQL
```
[root@localhost ~]# tar xvf mysql-5.6.37.tar.gz
[root@localhost ~]# cd mysql-5.6.37
[root@localhost ~]# cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/tdoa/mysql \
-DMYSQL_DATADIR=/usr/local/tdoa/data5 \
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DEXTRA_CHARSETS=all \
-DDEFAULT_COLLATION=utf8_general_ci \
-DDEFAULT_CHARSET=utf8  \
-DMYSQL_TCP_PORT=3336 \
-DWITH_READLINE=1 \
-DENABLED_LOCAL_INFILE=1  \
-DMYSQL_USER=mysql \
-DWITH_DEBUG=0
[root@localhost ~]# make
[root@localhost ~]# make install
[root@localhost ~]# cp /usr/local/mysql/support-files/my-default.cnf  /etc/my.cnf
[root@localhost ~]# cat /etc/my.cnf.old > /etc/my.cnf
[root@localhost ~]# cp -p /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysql
[root@localhost ~]# /usr/local/mysql/scripts/mysql_install_db --basedir=/usr/local/mysql --datadir=/usr/local/data5
[root@localhost ~]# chown mysql.mysql -R /usr/local/tdoa/mysql
[root@localhost ~]# chown mysql.mysql -R /usr/local/tdoa/data5
[root@localhost ~]# /etc/init.d/mysql start
[root@localhost ~]# ps -ef |grep mysql
[root@localhost ~]# /etc/init.d/mysql stop
[root@localhost ~]# /usr/local/data5 /usr/local/data5.newold
[root@localhost ~]# mv /usr/local/data5.old /usr/local/data5
[root@localhost ~]# /etc/init.d/mysql start
[root@localhost ~]# /usr/local/mysql/bin/mysql_upgrade -uroot -pxxxx
```
升级后的MySQL版本
```
[root@localhost ~]# /usr/local/tdoa/mysql/bin/mysql -V
/usr/local/tdoa/mysql/bin/mysql  Ver 14.14 Distrib 5.6.37, for Linux (x86_64) using  EditLine wrapper
```
请保证一些路径的设置和以前的my.cnf一致，如果设置错误，很可能导致启动不了数据库
[ERROR] Could not use /usr/local/data5/slowlog/slow.log for logging
我这里是由于慢日志的位置和老的mysql5.6路径不一致，创建/data/slowlog即可，这里要仔细看报错，然后根据报错来发现和解决问题。
