# Nginx平滑升级
[点击下载Nginx_1.12.1:](http://nginx.org/download/nginx-1.12.1.tar.gz)  
nginx版本：1.8.0  
nginx升级版本:1.12.1  
nginx -v   #查看当前Nginx版本  
**nginx -V   #查看当前Nginx编译参数**

![](assets/003/20180131-e12dcb29.png)  
**在执行编译时会报错没有--with-http_spdy_module选项（nginx 1.9.5 已经没有了 --with-http_spdy_module ，取代的是 --with-http_v2_module），查看官方模块地址：http://nginx.org/en/docs/**

![](assets/003/20180131-72daee0d.png)  
**开始部署**
```
[root@localhost src]#tar xvf nginx-1.12.1.tar.gz
[root@localhost nginx-1.12.1]# cd nginx-1.12.1
[root@localhost nginx-1.12.1]# ./configure
--user=tdoa
--group=tdoa
--prefix=/usr/local/tdoa/nginx
--with-file-aio
--with-http_stub_status_module
--with-http_gzip_static_module
--with-pcre=/root/linux-2015/down/pcre-8.32
--with-pcre-jit
--with-http_v2_module
[root@localhost nginx-1.12.1]# make
```
**执行make后，make install无需执行，将现有nginx重命名**
```
[root@localhost objs]# mv /usr/local/tdoa/nginx/sbin/nginx  /usr/local/tdoa/nginx/sbin/nginx.old
[root@localhost objs]# pwd
/usr/local/src/nginx-1.12.1/objs
[root@localhost objs]# cp nginx
nginx    nginx.8  
[root@localhost objs]# cp nginx /usr/local/tdoa/nginx/sbin/
[root@localhost objs]# /usr/local/tdoa/nginx/sbin/nginx -v
nginx version: nginx/1.12.1
[root@localhost objs]# /usr/local/tdoa/nginx/sbin/nginx -t
nginx: the configuration file /usr/local/tdoa/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/tdoa/nginx/conf/nginx.conf test is successful
[root@localhost objs]# /usr/local/tdoa/nginx/sbin/nginx -s reload
[root@localhost objs]# netstat  -tnlp |grep 80
tcp        0      0 0.0.0.0:80                  0.0.0.0:*                   LISTEN      98457/nginx         
tcp        0      0 127.0.0.1:631               0.0.0.0:*                   LISTEN      1680/cupsd          
tcp        0      0 ::1:631                     :::*                        LISTEN      1680/cupsd
```
