# Test1



性能观测工具

性能压测工具

性能调优工具

mem

-/&#43; buffers/cache：
-buffers/cache 的内存数：95 (等于第1行的 used - buffers - cached)
&#43;buffers/cache 的内存数: 32 (等于第1行的 free &#43; buffers &#43; cached)
1. -buffers/cache反映的是被程序实实在在吃掉的内存，
2. &#43;buffers/cache反映的是可以挪用的内存总数。

ssl

# acme.sh
# 默认是zerossl，存在认证问题：get authz objec with invalid status, please try again later.
# https://github.com/acmesh-official/acme.sh/wiki/Server
acme.sh --set-default-ca --server letsencrypt
# 申明dns的aksk
export DP_Id=&#34;xxx&#34;;export DP_Key=&#34;xxx&#34;
# 生成需要的泛域名证书
acme.sh --dnssleep --force --issue -k 2048 --dns dns_dp -d &#39;*.exsample.com&#39; -d exsample.com
# 生成nginx 证书
acme.sh --force --install-cert -d *.xxx.net \
--key-file /usr/local/openresty/nginx/conf/ssl/xxx.net.key \
--fullchain-file /usr/local/openresty/nginx/conf/ssl/xxx.net.pem \
--reloadcmd &#34;nginx -s reload&#34;
# 删除证书
acme.sh --remove -d *.exsample.com
# 查看证书信息
acme.sh --info -d *.exsample.com
# 查看证书列表
acme.sh --list
# 设置自动更新
acme.sh --upgrade --auto-upgrade

minikube

minikube start --image-mirror-country=&#39;cn&#39; --driver=hyperkit --registry-mirror=&#34;&lt;https://wtkw45ta.mirror.aliyuncs.com&gt;&#34;

certbot

certbot certonly --manual  --preferred-challenges dns

Linux Ctrl C无效

原因：rvm 版本bug
解决方法：
命令查看：
正常：
[root@server002 ~]# trap
trap -- &#39;&#39; SIGTSTP
trap -- &#39;&#39; SIGTTIN
trap -- &#39;&#39; SIGTTOU
异常：
[root@server002 ~]# trap
trap -- &#39;&#39; SIGTSTP
trap -- &#39;&#39; SIGTTIN
trap -- &#39;&#39; SIGTTOU
trap -- &#39;&#39; SIGINT
trap -- &#39;&#39; SIGQUIT
现象：终端Ctrl &#43; C完全失效，当执行trap 信号命令时多处两个SIGINT和SIGQUIT两项
升级rvm 版本：rvm get stable（
1.29.4
版本以上都可以解决）
卸载rvm工具：gem uninstall rvm

pip

● 国内源临时加速

pip install markdown -i &lt;https://pypi.tuna.tsinghua.edu.cn/simple&gt;

● 永久配置

# 清华源
pip config set global.index-url &lt;https://pypi.tuna.tsinghua.edu.cn/simple&gt;
# 或：
# 阿里源
pip config set global.index-url &lt;https://mirrors.aliyun.com/pypi/simple/&gt;
# 腾讯源
pip config set global.index-url &lt;http://mirrors.cloud.tencent.com/pypi/simple&gt;
# 豆瓣源
pip config set global.index-url &lt;http://pypi.douban.com/simple/&gt;

goaccess

sed -n &#39;/2019-12-18T07:30:00&#43;08:00/,/2019-12-18T08:30:00&#43;08:00/p&#39; nginx.access.main.20191218.log &gt;&gt;nginx01.log

goaccess -f {nginx3.log,nginx1.log,nginx2.log,nginx4.log} -a -d -p /usr/local/etc/goaccess.conf -o nginx0730-0830.html &amp;

goaccess -f nginx3.log -a -d -p /root/goaccess.conf -o nginx.html &amp;

conda

• 安装uwsgi：
conda install -c conda-forge uwsgi
# 取消自动进入base环境
conda config --set auto_activate_base false

Java

• Jcmd 开启java 进程remote 端口
jcmd 20364 ManagementAgent.start jmxremote.port=9999 jmxremote.ssl=false jmxremote.authenticate=false

-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9999  -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false  -Djava.rmi.server.hostname=10.1.219.21
• Java dump堆栈信息
jmap -dump:format=b,file=VipQuickRoutePlatAsyn.dat 29473

mac ssh config

Host *
ServerAliveInterval 60
ServerAliveCountMax 30
HostkeyAlgorithms &#43;ssh-rsa
PubkeyAcceptedAlgorithms &#43;ssh-rsa

lvm

lvm
pvcreate /dev/vdc
vgextend VolGroup /dev/vdc
lvextend -l &#43;100%FREE /dev/mapper/VolGroup-LogVol00
resize2fs /dev/mapper/VolGroup-LogVol00

shell

●  根据日期排序删除
ls -ltd FilePath | awk &#39;{if(NR&gt;10){print $0}}&#39; | xargs rm -rf;

●  在线扩容
○ 安装工具包：
yum  -y install cloud-utils-growpart
○ 给指定分区扩容：
growpart /dev/vda 1
○ 扩容支文件系统（如果无法resize，确保分区已扩容的情况下重启服务器离线扩容）：
resize2fs /dev/vda1
●  shell 判断
-eq # 等于
-ne # 不等于
-gt # 大于
-lt # 小于
-ge # 大于等于
-le # 小于等于

●  timewait查看
netstat -an | awk &#39;{print $6}&#39; | sort | uniq -c | sort -nr

●  ssh config
StrictHostKeyChecking no
Host    code.szy.net
HostName        172.16.200.5
Port            8022
User            root
IdentityFile    ~/.ssh/id_rsa

Host 47.96.134.74
HostName 47.96.134.74
User root
IdentityFile    ~/.ssh/id_rsa

●  路由新增删除
route add -net  106.15.100.0/24   gw 183.57.42.65
route del -net  106.15.100.0/24   gw 183.57.42.65

●  maildrop删除
rsync  --delete -rlptD /tmp/empty/ /var/spool/postfix/maildrop/

●  ssh服务跳过公钥认证
ServerAliveInterval 60
ServerAliveCountMax 30
HostkeyAlgorithms &#43;ssh-rsa
PubkeyAcceptedAlgorithms &#43;ssh-rsa
UserKnownHostsFile=/dev/null
StrictHostKeyChecking no
UserKnownHostsFile=/dev/null

●  crontab 使用vim编辑
1.对于一些系统，crontab的默认编译器使用起来不是很方便，想换成熟悉的vim，按下面操作即可：编辑.profile文件，增加EDITOR=vim;export EDITOR即可；
2.在命令行直接输入EDITOR=vim;export EDITOR

●  IP地址判断
((2(5[0-5]|[0-4]\\\\d))|[0-1]?\\\\d{1,2})(\\\\.((2(5[0-5]|[0-4]\\\\d))|[0-1]?\\\\d{1,2})){3}

●  ossutil
/opt/ossutil64 cp ./cp_coupon_code_bak2.sql.gz oss://mw-coldbackup/backup_data/10.1.127.87/20221104/ -u -c /root/oss.cold

●  多线程压缩 pigz
# 压缩文件
pigz -k filename # -k 保留原文件
pigz -l filename.gz # -l 查看文件压缩内容
# 压缩目录
tar --use-compress-program=&#34;pigz -k&#34; -cvf dir1.tar.gz dir1
# 解压文件
pigz -k -d filename.gz
# 解压目录
tar --use-compress-program=&#34;pigz -k &#34; -xvf dir1.tar.gz

●  requirements 依赖生成: pipreqs --encoding utf8 --force
openssl
● 证书测试
openssl s_client -connect domainName:443
● 强密码生成
openssl rand -base64 15

---

> 作者:   
> URL: http://localhost:1313/posts/test1/  

