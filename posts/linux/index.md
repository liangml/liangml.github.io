# Linux


## mem centos6.5
```text
-/+ buffers/cache：
-buffers/cache 的内存数：95 (等于第1行的 used - buffers - cached)
+buffers/cache 的内存数: 32 (等于第1行的 free + buffers + cached)
1. -buffers/cache 反映的是被程序实实在在吃掉的内存，
2. +buffers/cache 反映的是可以挪用的内存总数。
```
## Linux Ctrl C无效
```text
原因：rvm 版本bug
解决方法：
命令查看：
    正常：
            [root@server002 ~]# trap
            trap -- '' SIGTSTP
            trap -- '' SIGTTIN
            trap -- '' SIGTTOU
    异常：
            [root@server002 ~]# trap
            trap -- '' SIGTSTP
            trap -- '' SIGTTIN
            trap -- '' SIGTTOU
            trap -- '' SIGINT
            trap -- '' SIGQUIT
       现象：终端Ctrl + C完全失效，当执行trap 信号命令时多处两个SIGINT和SIGQUIT两项
升级rvm 版本：rvm get stable（
1.29.4
版本以上都可以解决）
卸载rvm工具：gem uninstall rvm
```
## pip
+ 国内源临时加速
```bash
pip install markdown -i <https://pypi.tuna.tsinghua.edu.cn/simple>
```
+ 永久配置
```bash
# 清华源
pip config set global.index-url <https://pypi.tuna.tsinghua.edu.cn/simple>
# 或：
# 阿里源
pip config set global.index-url <https://mirrors.aliyun.com/pypi/simple/>
# 腾讯源
pip config set global.index-url <http://mirrors.cloud.tencent.com/pypi/simple>
```
## goaccess
## conda
```bash
• 安装uwsgi：
conda install -c conda-forge uwsgi
# 取消自动进入base环境
conda config --set auto_activate_base false
```
## lvm
```plain
lvm
pvcreate /dev/vdc
vgextend VolGroup /dev/vdc
lvextend -l +100%FREE /dev/mapper/VolGroup-LogVol00
resize2fs /dev/mapper/VolGroup-LogVol00
```
## 根据日期排序删除
```plain
ls -ltd FilePath | awk '{if(NR>10){print $0}}' | xargs rm -rf;
```
## 磁盘在线扩容
* 安装工具包：
```shell
yum  -y install cloud-utils-growpart
```
* 给指定分区扩容：
```shell
growpart /dev/vda 1
```
* 扩容支文件系统（如果无法resize，确保分区已扩容的情况下重启服务器离线扩容）：

```shell
resize2fs /dev/vda1
```
## timewait查看

```plain
netstat -an | awk '{print $6}' | sort | uniq -c | sort -nr
```
## 路由新增删除
```plain
route add -net  106.15.100.0/24   gw 183.57.42.65
route del -net  106.15.100.0/24   gw 183.57.42.65
```
## maildrop删除

```shell
rsync  --delete -rlptD /tmp/empty/ /var/spool/postfix/maildrop/
```
## ssh config
```plain
ServerAliveInterval 60
ServerAliveCountMax 30
HostkeyAlgorithms +ssh-rsa
PubkeyAcceptedAlgorithms +ssh-rsa
UserKnownHostsFile=/dev/null
StrictHostKeyChecking no
UserKnownHostsFile=/dev/null
```
---
```plain
Host *
 ServerAliveInterval 60
 ServerAliveCountMax 30
 HostkeyAlgorithms +ssh-rsa
 PubkeyAcceptedAlgorithms +ssh-rsa
 StrictHostKeyChecking no
```
---
```text
Host IPADDRESS
    HostName IPADDRESS
    User root
    IdentityFile    ~/.ssh/id_rsa
```
## crontab 使用vim编辑

```plain
  1.对于一些系统，crontab的默认编译器使用起来不是很方便，想换成熟悉的vim，按下面操作即可：编辑.profile文件，增加EDITOR=vim;export EDITOR即可；
  2.在命令行直接输入EDITOR=vim;export EDITOR
```
##  ossutil
```plain
/opt/ossutil64 cp xxx.sql.gz oss://BUCKET/dir/ -u -c CONFIGFILE
```
## 多线程压缩 pigz

```plain
# 压缩文件
pigz -k filename # -k 保留原文件
pigz -l filename.gz # -l 查看文件压缩内容
# 压缩目录
tar --use-compress-program="pigz -k" -cvf dir1.tar.gz dir1
# 解压文件
pigz -k -d filename.gz
# 解压目录
tar --use-compress-program="pigz -k " -xvf dir1.tar.gz
```



## requirements 依赖生成: 
```shell
pipreqs --encoding utf8 --force
```

## openssl
+ 证书测试

```bash
openssl s_client -connect domainName:443
```

## 强密码生成

```bash
openssl rand -base64 15
```
* 系统使用规范(自有服务统一授权为普通用户权限)
    * 统一数据目录：/data
    * 统一日志目录：/var/log
    * 应用统一管理工具：supervisor
    * 三方应用目录：/opt
    * 示例服务标记：_应用目录：application 应用备份：appbak 公共脚本：scripts_

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/linux/  

