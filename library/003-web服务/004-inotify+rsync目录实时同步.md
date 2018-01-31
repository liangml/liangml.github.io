#inotify+rsync安装部署
##检查是否安装xinetd、rsync是否安装
```
[root@IM-web-01 ~] rpm -qa |grep xinetd rsync    #查看xinetd、rsync是否安装
[root@IM-web-01 ~] yum -y install xinetd rsync   #如果没有安装则yum安装两个服务
[root@IM-web-01 ~] chkconfig xinetd on           #添加xinetd自启动
[root@IM-web-01 ~] chkconfig rsync on            #添加rsync自启动                   
[root@IM-web-01 ~] /etc/init.d/xinetd start      #启动xinetd守护进程服务
[root@IM-web-01 ~] useradd www -s /sbin/nologin  #创建同步rsync同步用户
[root@IM-web-01 ~] chown www.www /usr/local/nginx/html
```
###rsync服务端配置文件样例
```
[root@IM-web-01 ~] vim /etc/rsyncd.conf
uid = www
gid = www
use chroot = no
max connections = 200
timeout = 300
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsyncd.log
[web]
path = /usr/local/nginx/html/
ignore errors
read only = no
write only = no
list = false
hosts allow = 10.0.11.0/24
hosts deny = 0.0.0.0/32
uid = www
gid = www
secrets file = /etc/rsync.password

编写密码文件:
[root@IM-web-01 ~] echo "www:www" > /etc/rsync.password #创建配置服务端密码文件
[root@IM-web-01 ~] chmod 600 /etc/rsync.password        #配置密码文件为只读模式,增加安全性
[root@IM-web-01 ~] rsync --daemon                       #后台启动rsync
```
##inotify、rsync客户端安装配置
GitHub地址：https://github.com/rvoicilas/inotify-tools/wiki
下载地址：http://github.com/downloads/rvoicilas/inotify-tools/inotify-tools-3.14.tar.gz
上传服务器进行安装部署
[root@IM-web-01 ~] tar xvf inotify-tools-3.14.tar.gz
[root@IM-web-01 ~] cd inotify-tools-3.14
[root@IM-web-01 ~] ./configure --prefix=/usr/local/inotify
[root@IM-web-01 ~] make && make install
创建rsync密码文件并配置只读模式
[root@IM-web-01 ~] echo "www" > /etc/rsync.password　　　　　#切记这里只写密码即可
[root@IM-web-01 ~] chmod 600 /etc/rsync.password
###检查当前系统是否支持inotify
```
[root@IM-web-01 ~]# ll /proc/sys/fs/inotify/
总用量 0
-rw-r--r-- 1 root root 0 7月  10 20:28 max_queued_events
-rw-r--r-- 1 root root 0 7月  10 20:28 max_user_instances
-rw-r--r-- 1 root root 0 7月  10 20:28 max_user_watches
拓展：
 /proc/sys/fs/inotify/max_queued_evnets      
表示调用inotify_init时分配给inotify instance中可排队的event的数目的最大值，超出这个值的事件被丢弃，但会触发IN_Q_OVERFLOW事件。
/proc/sys/fs/inotify/max_user_instances
表示每一个real user ID可创建的inotify instatnces的数量上限。
/proc/sys/fs/inotify/max_user_watches
表示每个inotify instatnces可监控的最大目录数量。如果监控的文件数目巨大，需要根据情况，适当增加此值的大小。
例如： echo 30000000 > /proc/sys/fs/inotify/max_user_watches
```
###inotify命令讲解
```
[root@IM-web-01 inotify]# ./bin/inotifywait --help
-r|--recursive   Watch directories recursively. #递归查询目录
-q|--quiet      Print less (only print events). #打印监控事件的信息
-m|--monitor   Keep listening for events forever.  Without this option, inotifywait will exit after one  event is received.        #始终保持事件监听状态
--excludei <pattern>  Like --exclude but case insensitive.    #排除文件或目录时，不区分大小写。
--timefmt <fmt> strftime-compatible format string for use with %T in --format string. #指定时间输出的格式
--format <fmt>  Print using a specified printf-like format string; read the man page for more details.
#打印使用指定的输出类似格式字符串
-e|--event <event1> [ -e|--event <event2> ... ] Listen for specific event(s).  If omitted, all events are  listened for.   #通过此参数可以指定需要监控的事件，如下所示:
Events：
access           file or directory contents were read       #文件或目录被读取。
modify           file or directory contents were written    #文件或目录内容被修改。
attrib            file or directory attributes changed      #文件或目录属性被改变。
close            file or directory closed, regardless of read/write mode    #文件或目录封闭，无论读/写模式。
open            file or directory opened                    #文件或目录被打开。
moved_to        file or directory moved to watched directory    #文件或目录被移动至另外一个目录。
move            file or directory moved to or from watched directory    #文件或目录被移动另一个目录或从另一个目录移动至当前目录。
create           file or directory created within watched directory     #文件或目录被创建在当前目录
delete           file or directory deleted within watched directory     #文件或目录被删除
unmount         file system containing file or directory unmounted  #文件系统被卸载
```
###inotify实时同步脚本样例
```
#!/bin/bash
#by liangml
host02=10.0.11.157
host03=10.0.11.158
host04=10.0.11.159
src=/usr/local/nginx/html/
dst=web
user=www
rsync_passfile=/etc/rsync.password
inotify_home=/usr/local/inotify
#judge
if [ ! -e "$src" ] \
|| [ ! -e "${rsync_passfile}" ] \
|| [ ! -e "${inotify_home}/bin/inotifywait" ] \
|| [ ! -e "/usr/bin/rsync" ];
then
echo "Check File and Folder"
exit 9
fi
${inotify_home}/bin/inotifywait -mrq --timefmt '%d/%m/%y %H:%M' --format '%T %w%f' -e close_write,delete,create,attrib $sr
c \
| while read file
do
#  rsync -avzP --delete --timeout=100 --password-file=${rsync_passfile} $src $user@$host01::$dst >/dev/null 2>&1
cd $src && rsync -aruz -R --delete ./  --timeout=100 $user@$host02::$dst --password-file=${rsync_passfile} >/dev/null 2>&1
cd $src && rsync -aruz -R --delete ./  --timeout=100 $user@$host03::$dst --password-file=${rsync_passfile} >/dev/null 2>&1
cd $src && rsync -aruz -R --delete ./  --timeout=100 $user@$host04::$dst --password-file=${rsync_passfile} >/dev/null 2>&1
done
exit 0
```
将同步文件后台运行echo "nohup sh inotify.sh &" >> /etc/rc.local
##测试：
[root@IM-web-01 ~] rsync -avz test.txt www@10.0.11.157::web --password-file=/etc/rsync.password
