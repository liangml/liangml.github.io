#keepalived安装部署
下载最新软件：http://www.keepalived.org/software/keepalived-1.3.5.tar.gz
上传服务器安装：
```
tar xvf http://www.keepalived.org/software/keepalived-1.3.5.tar.gz
cd keepalived-1.3.5
./configure --prefix=/usr/local/keepalived
make && make install
ln -s /usr/local/keepalived/sbin/ /usr/bin/
ln -s /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
cp /root/keepalived-1.3.5/keepalived/etc/init.d/keepalived.rh.init /etc/init.d/keepalived
mkdir  /etc/keepalived
echo 1 > /proc/sys/net/ipv4/ip_forward　　　　　　#开启路由转发功能
echo 1 > /proc/sys/net/ipv4/ip_nonlocal_bind    #开启允许绑定非本机IP
```
##配置文件样例
```
! Configuration File for keepalived
global_defs {
   notification_email {
     liangml0528@163.com
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 127.0.0.1
   smtp_connect_timeout 30
   router_id LVS_DEVEL
}
vrrp_script chk_http_port {
                script "/root/check_haproxy.sh"
                interval 2
                weight 2
}

vrrp_instance VI_1 {
    state MASTER
    interface eth1
    virtual_router_id 51
    priority 100
    advert_int 1
    mcast_src_ip 10.0.11.154
    authentication {
        auth_type PASS
        auth_pass 1111
    }
track_script {
       chk_http_port
    }
    virtual_ipaddress {
       10.0.11.16/24
    }
}
```
##haproxy检查脚本（需要添加执行权限）
```
#!/bin/bash
status=`ps aux|grep haproxy | grep -v grep | grep -v bash | wc -l`
if [ "${status}" = "0" ]; then
    /etc/init.d/haproxy start
fi
status2=`ps aux|grep haproxy | grep -v grep | grep -v bash |wc -l`
if [ "${status2}" = "0"  ]; then
    /etc/init.d/keepalived stop
fi
```
##日志添加
默认日志存放在系统的/var/log/messages下
把日志单独存放需要修改/etc/sysconfig/keepalived  
![](assets/003/20180131-cdb25d12.png)  
**在/etc/rsyslog.conf**末尾添加  
![](assets/003/20180131-6a656269.png)  
**重启日志服务**
/etc/init.d/rsyslog restart
**重启keepalived**
/etc/init.d/keepalived
**查看日志**  
![](assets/003/20180131-8af44a1e.png)  
