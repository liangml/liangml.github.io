# 

# Nginx:
nginx 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。  
Nginx  是为俄罗斯访问量第二的 Rambler.ru 站点开发的，第一个公开版本0.1.0发布于2004年10月4日。因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。  
```shell
1.配yum，安装FTP，上传pcre,nginx软件
　　yum -y install gcc gcc-c&#43;&#43; make libtool 安装编译工具
　　service vsftpd start
　　上传pcre-....      nginx-..........
　　PCRE:perl兼容的正则表达式库
2.安装相关开发库
　　yum -y install glib2-devel openssl-devel pcre-devel bzip2-devel gzip-devel  perl-devel ·
3.安装pcre库
　　tar xvf pcre-8.33.tar.gz
　　cd pcre-...
　　./configure --prefix=/usr/local/pcre
　　make &amp;&amp;make install
4.安装nginx
　　tar zxcf nginx-1.4.4.....tar.gz
　　cd nginx-...
　　./configure --prefix=/usr/local/nginx
　　make &amp;&amp;make install
5.配置nginx
　　/usr/local/nginx/sbin/nginx 开启nginx服务
　　测试  
　　显示welcome to nginx 服务正常
　　网站根目录/usr/local/nginx/html
　　主页index.html
　　主配置文件/usr/local/nginx/conf/nginx.conf
# 基于端口的虚拟主机
　　主配置文件/usr/local/nginx/conf/nginx.conf
```
```sehll
　server {
　　listen 80;   
　　root /www/local;
　　}
　......
　server {
　　listen 8080;   
　　root /www/local;
　　}
```
# 基于IP的虚拟主机
　主配置文件/usr/local/nginx/conf/nginx.conf
```shell
　server {
　　listen 192.168.1.100:80;   
　　root /www/local;
　　index index.html;
　}
......
　server {
　　listen 192.168.1.110:80;   
　　root /www/local;
　　index index.html;
　}
```
# 基于域名的虚拟主机且做访问控制
　主配置文件/usr/local/nginx/conf/nginx.conf
```shell
　server {
　　listen 80;   
　　server_name www.local.com;
　　location / {
　　　root /www/local;
　　　index index.html index.htm;
　　　auth_basic &#34;***&#34;;#访问控制
　　　auth_basic_user_file /usr/local/nginx/passwd.db;
　　　}
　　}
......
　　server {
　　　listen 80;   
　　　server_name www.local.com;
　　　location / {
　　　　root /www/local;
　　　　index index.html index.htm;
　　　}
　　　}
```
　htpasswd -c /usr/local/nginx/passwd.db tom 输入密码
　由于成本（IP）和客户使用习惯（端口）的限制，相对没有基于域名的虚拟主机多
　为Nginx建立用户  ：
　/usr/sbin/useradd -c &#34;Nginx user&#34; -s /bin/false -r -d /var/lib/nginx nginx  
　将nginx添加到认证文件中并设置登录网站的密码：
　htpasswd  /usr/local/nginx/passwd.db nginx
　单独创建虚拟主机的配置文件
　主配置文件/usr/local/nginx/conf/nginx.conf
```shell
　server {
　　listen  80;
　　servername  
　　location /{
　　root html 相对路径，变绝对路径要加/
　　index
　　　}
　　}
　http {
　　include   vhost.conf; 虚拟主机的主配置文件可以单独创建，默认存放在当前路径   
。。。。
　}
#新建vhost.conf

　server {
　　listen 80;  改成80端口
　　#listen somename:8080;
　　server_name  somename alias another.alias;（www.wg.com）
　　location / {
　　　root /www/wg;
　　　index index.html index.htm;
　　　}
　　}
```
　一个虚拟主机配置完成，更多的虚拟主机，只需重复粘贴在此文件中，改域名路径即可
　基于域名的虚拟主机，配置DNS
　分别写各网站主页
　重启nginx:
　(1) killall nginx 结束nginx进程
　(2) /usr/local/nginx/sbin/nginx 开启nginx服务
　测试
# 配置nginx负载均衡
　客户端的请求通过 proxy_pass 指令传送给后端的服务器，从而实现负载均衡。
　vim /usr/local/nginx/conf/nginx.conf
```shell
　server {
  　  listen  80;  
    　location /{
    　   proxy_pass http://nlb ;反向代理，
    　   }
　　}

　#将请求分配给后端的两台服务器以及自身的8080端口，实现负载均衡
　upstream nlb{
           server 127.0.0.1:8080 weight=2;  
　　　#weight：权重值
           server 172.18.10.101 weight=1;
           server 172.18.10.102 weight=1;
     }
　#本机8080端口站点：/usr/local/nginx/nlb
　server {
　　Listen  8080;
　　 root /usr/local/nginx/nlb；
　　}
```
在 Nginx 的集群配置中，Nginx 使用最简单的平均分配规则给集群中的每个节点分配请求。一旦某个节点失效时，或者重新起效时，Nginx 都会非常及时的处理状态的变化，以保证不会影响到用户的访问。
动态页面请求处理
　　Nginx 本身并不支持现在流行的 JSP、ASP、PHP、PERL 等动态页面，但是它可以通过反向代理将请求发送到后端的服务器，例如 Tomcat、Apache、IIS 等来完成动态页面的请求处理。方法同上。


---

> 作者: Leon  
> URL: https://leon.gitee.com/posts/  

