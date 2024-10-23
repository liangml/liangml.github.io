# Alibabalinux

&#43; 系统版本：Alibaba Cloud Linux 3.2104 LTS 64位
&#43; 默认镜像操作
```bash
# 关闭默认启动服务
systemctl disable nfs-server
systemctl disable rpc-statd
systemctl disable systemd-resolved
systemctl disable rpcbind
systemctl disable nfsdcld

systemctl stop nfs-server
systemctl stop rpc-statd
systemctl stop rpcbind
systemctl stop systemd-resolved
systemctl stop nfsdcld

# 安装软件
dnf install supervisor
dnf install nscd

# 新增普通用户
useradd xxx
```
* 清除dns缓存
```bash
service nscd restart
# 或者使用以下命令清楚
service nscd restart
```
&#43;  crontab
```bash
59 23 * * * /usr/sbin/logrotate /etc/logrotate.conf
```
&#43; logrostate
```bash
daily                     #指定转储周期为每天
weekly                    #指定转储周期为每周；
monthly                   #指定转储周期为每月；
rotate count              #指定日志文件删除之前转储的次数，0指没有备份，5指保留5个备份；
compress                  #通过gzip压缩转储以后的日志；
nocompress                #不需要压缩时，用这个参数；
delaycompress             #延迟压缩，和compress一起使用时，转储的日志文件到下一次转储时才压缩；
nodelaycompress           #覆盖delaycompress选项，转储同时压缩；
copytruncate              #用于还在打开中的日志文件，把当前日志备份并截断；
nocopytruncate            #备份日志文件但是不截断；
create mode owner group   #转储文件，使用指定的文件模式创建新的日志文件；
nocreate                  #不建立新的日志文件；
errors address            #专储时的错误信息发送到指定的Email地址；
ifempty                   #即使是空文件也转储，这个是logrotate的缺省选项；
notifempty                #如果是空文件的话，不转储；
mail address              #把转储的日志文件发送到指定的E-mail地；
nomail                    #转储时不发送日志文件；
olddir directory          #转储后的日志文件放入指定的目录，必须和当前日志文件在同一个文件系统；
noolddir                  #转储后的日志文件和当前日志文件放在同一个目录下；
prerotate/endscript       #在转储以前需要执行的命令可以放入这个对，这两个关键字必须单独成行；
postrotate/endscript      #在转储以后需要执行的命令可以放入这个对，这两个关键字必须单独成行；
tabootext [&#43;] list        #让logrotate不转储指定扩展名的文件，缺省的扩展名是：.rpm-orig, .rpmsave,v,和~ ；
size size                 #当日志文件到达指定的大小时才转储，Size可以指定bytes(缺省)以及KB(sizek)或者MB(sizem)；
postrotate                #日志轮换过后指定指定的脚本，endscript参数表示结束脚本；
sharedscripts             #共享脚本,下面的postrotate中的脚本只执行一次即可；
```

---

```bash
# 手动执行：/usr/sbin/logrotate -f /etc/logrotate.conf
# nginx
/var/log/nginx/*.log {
        daily
        dateext
        missingok
        rotate 52
        compress
        delaycompress
        notifempty
        create 640 nginx zabbix
        sharedscripts
        postrotate
                /usr/sbin/nginx -s reload
        endscript
}
# 示例：待验证
/var/log/nginx/*log {
    daily
    rotate 10
    missingok
    notifempty
    compress
    sharedscripts
    su root
    postrotate
        /bin/kill -USR1 $(cat /var/run/nginx.pid 2&gt;/dev/null) 2&gt;/dev/null || :
    endscript
}
# httpd
/var/log/httpd/*log {
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
        /sbin/service httpd reload &gt; /dev/null 2&gt;/dev/null || true
    endscript
}
```
* sshd_config配置
```bash
# alibaba linux default configure
UseDNS no
AddressFamily inet
SyslogFacility AUTHPRIV
PermitRootLogin yes
PasswordAuthentication yes
```

```plain
https://help.aliyun.com/zh/ecs/how-to-enable-the-kdump-service-for-linux-instances?spm=a2c4g.750001.0.i42
```



---

> 作者: Leon  
> URL: https://liangml.github.io/posts/alibabalinux/  

