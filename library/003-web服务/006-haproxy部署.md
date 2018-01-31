#HAproxy安装部署
下载地址：http://www.haproxy.org/download/1.7/src/haproxy-1.7.8.tar.gz
下载后安装上传服务器安装：
```
tar xvf haproxy-1.7.8.tar.gz
cd haproxy-1.7.8
make TARGET=linux2632 PREFIX=/usr/local/haproxy
make install PREFIX=/usr/local/haproxy
mkdir -p /usr/local/haproxy/conf
```
##haproxy启动脚本样例
```
#!/bin/bash
#
# haproxy
#
# chkconfig: 35 85 15
# description: HAProxy is a free, very fast and reliable solution \
# offering high availability, load balancing, and \
# proxying for TCP and HTTP-based applications
# processname: haproxy
# config: /etc/haproxy.cfg
# pidfile: /var/run/haproxy.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

config="/usr/local/haproxy/conf/haproxy.cfg"
exec="/usr/local/haproxy/sbin/haproxy"
prog=$(basename $exec)

[ -e /etc/sysconfig/$prog ] && . /etc/sysconfig/$prog

lockfile=/var/lock/subsys/haproxy

check() {
    $exec -c -V -f $config
}

start() {
    $exec -c -q -f $config
    if [ $? -ne 0 ]; then
        echo "Errors in configuration file, check with $prog check."
        return 1
    fi

    echo -n $"Starting $prog: "
    # start it up here, usually something like "daemon $exec"
    daemon $exec -D -f $config -p /var/run/$prog.pid
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    # stop it here, often "killproc $prog"
    killproc $prog
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    $exec -c -q -f $config
    if [ $? -ne 0 ]; then
        echo "Errors in configuration file, check with $prog check."
        return 1
    fi
    stop
    start
}

reload() {
    $exec -c -q -f $config
    if [ $? -ne 0 ]; then
        echo "Errors in configuration file, check with $prog check."
        return 1
    fi
    echo -n $"Reloading $prog: "
    $exec -D -f $config -p /var/run/$prog.pid -sf $(cat /var/run/$prog.pid)
    retval=$?
    echo
    return $retval
}

force_reload() {
    restart
}

fdr_status() {
    status $prog
}

case "$1" in
    start|stop|restart|reload)
        $1
        ;;
    force-reload)
        force_reload
        ;;
    checkconfig)
        check
        ;;
    status)
        fdr_status
        ;;
    condrestart|try-restart)
      [ ! -f $lockfile ] || restart
    ;;
    *)
        echo $"Usage: $0 {start|stop|status|checkconfig|restart|try-restart|reload|force-reload}"
        exit 2
esac
```
##haproxy配置文件样例
```
global
log 127.0.0.1   local3
maxconn 65535
chroot /usr/local/haproxy
uid 1000
gid 1000
daemon
nbproc 1
pidfile /var/run/haproxy.pid

defaults
log     127.0.0.1       local3
mode    http
option  httplog
option  httpclose
option  dontlognull
option  forwardfor
option  redispatch
retries 2
maxconn 2000
balance roundrobin
stats   uri     /status
stats auth admin:admin123
timeout connect    5000
timeout client     50000
timeout server     50000
listen  web_proxy
        bind 0.0.0.0:80
        mode http
        balance roundrobin
        cookie SERVERID insert indirect nocache
        option httpclose
        option forwardfor
        option accept-invalid-http-request
        option httpchk HEAD /index.php HTTP/1.0
        server web01 10.0.11.156:80  weight 5 check inter 2000  fall 3
        server web02 10.0.11.157:80  weight 5 check inter 2000  fall 3
        server web03 10.0.11.158:80  weight 5 check inter 2000  fall 3
        server web04 10.0.11.159:80  weight 5 check inter 2000  fall 3
```
##haproxy日志添加
```
vim /etc/syslog.conf
添加：
local3.* /var/log/haproxy.log
local0.* /var/log/haproxy.log

vim /etc/sysconfig/syslog
修改：
SYSLOGD_OPTIONS="-r -m 0"
service syslog restart
```
