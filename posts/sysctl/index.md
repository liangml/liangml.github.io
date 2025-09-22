# Sysctl

## 备注:
* /proc/sys/：目录是Linux内核在启动后生成的伪目录，其目录下的net文件夹中存放了当前系统中开启的所有内核参数,目录树结构与参数的完整名称相关.
> 如: net.ipv4.tcp_tw_recycle，它对应的文件是/proc/sys/net/ipv4/tcp_tw_recycle文件，文件的内容就是参数值。
* 允许回收TCP连接，必须为1 Linux从4.12内核版本开始移除了 配置
```bash
net.ipv4.tcp_tw_recycle = 0
```
+  [阿里云 - Linux系统常用内核网络参数介绍与常见问题处理:](https://help.aliyun.com/knowledge_detail/41334.html)
* Sysctl 操作命令 
  * 查看当前生效的内核参数
```bash
sysctl -a
```
* 生效更改的内核参数
```bash
sysctl -p
```
+  禁用大内存页面 Transparent Huge Pages for Redis/MongoDB ，默认是 always
```bash
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
```
+  ulimits 优化设置打开文件的最大数量（文件描述符），按需修改最大数值。
> 编辑 /etc/security/limits.conf ，添加或替换下面几行代码到文件结尾.
```bash
root soft nofile 65535
root hard nofile 65535
# root hard nofile 65535
* soft     nproc          65535
* hard     nproc          65535
* soft     nofile         65535
* hard     nofile         65535
```
## sysctl.conf 优化
```bash
# 脏数据的比例和处理，根据场景不同设置，
# 参考 <https://lonesysadmin.net/2013/12/22/better-linux-disk-caching-performance-vm-dirty_ratio/>
# 如果是数据库服务器，希望数据能够尽快安全写入，可降低内存缓存比例
vm.dirty_background_ratio = 5
vm.dirty_ratio = 10

# 如果是业务服务器，对数据安全写入无要求，可加大内存缓存比例
vm.dirty_background_ratio = 50
vm.dirty_ratio = 80

# 设置为1，内核允许分配所有的物理内存,Redis常用
# 0， 表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
# 1， 表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
# 2， 表示内核允许分配超过所有物理内存和交换空间总和的内存
vm.overcommit_memory = 1
```

---

```bash
fs.file-max = 2097152

# 系统拥有的内存数，ElasticSearch启动必备
vm.max_map_count = 262144
# 数据包转发,0表示禁止
net.ipv4.ip_forward = 0
# 设置为1,tcp链接参数重新配置
net.ipv4.tcp_no_metrics_save = 1
# 处理无源路由
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
# 禁用 sysrq 功能
kernel.sysrq = 0
# 控制 core 文件的文件名中是否添加 pid 作为扩展
kernel.core_uses_pid = 1

# 服务端主动关闭后,客户端释放连接的超时,FIN-WAIT-2状态<30
net.ipv4.tcp_fin_timeout = 10
# 设置为1，防止 SYNC FLOOD 攻击
net.ipv4.tcp_syncookies = 1
# TIME_WAIT socket的最大数目，不宜太大或者太小，nginx反向代理必备
net.ipv4.tcp_max_tw_buckets = 262144

# 允许重用TCP连接,0表示关闭
net.ipv4.tcp_tw_reuse = 1
# 允许TCP保持的空闲keepalive时长,不需要太长
net.ipv4.tcp_keepalive_time = 30
# 链接失效前发送探测包数量
net.ipv4.tcp_keepalive_probes = 3
# 链接无确认时重新发送的频度,default 75s
net.ipv4.tcp_keepalive_intvl = 30

# 消息队列存放消息的总字节数
kernel.msgmnb = 65536
# 系统范围内最大多少个消息队列
kernel.msgmni = 2048
# 消息队列的最大消息大小，默认8k，建议64kb
kernel.msgmax = 65536
# 每个消息的最大size.
kernel.shmmax = 68719476736
# 内核参数定义单个共享内存段的最大值
kernel.shmall = 4294967296

# 交换内存使用
vm.swappiness = 0

# 系统作为TCP客户端连接自动使用的端口(start，end），可发起并发连接数为end-star
net.ipv4.ip_local_port_range = 1024 65500

# socket缓冲区发送和接收的默认值
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608
# tcp数据发送和接收值
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216

# TCP 缓冲区内存，连接数达到非常高时候需要配置好
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_mem = 786432 2097152 3145728

# 打开 SACK 选项,防止伪造sequence
net.ipv4.tcp_sack = 1
# 禁用timestamp，重要，高并发下设置为0
net.ipv4.tcp_timestamps = 0
# 激活窗口扩充因子，支持64kb以上数据传输 (2^30)1GB
net.ipv4.tcp_window_scaling = 1

# ACCEPT等待队列长度,当内核处理慢时多出的包放入网卡接受队列,反之为允许放入队列的最大数量
net.core.netdev_max_backlog = 262144
# 允许最大并发连接数，重要
net.core.somaxconn = 262144
# SYNC等待队列长度,太大了排队也没用
net.ipv4.tcp_max_syn_backlog = 262144
# 不属于任何进程的socket数目，不宜太大，防止攻击
net.ipv4.tcp_max_orphans = 262144
# 处于SYN_RECV状态时重传SYN+ACK包的次数,5以内
net.ipv4.tcp_synack_retries = 2
# 外向syn握手重试次数，5以内
net.ipv4.tcp_syn_retries = 2

# arp相邻层有效性周期
net.ipv4.neigh.default.gc_stale_time=120
# 数据包反向路由验证,0:关闭 1:严格 2:松散
net.ipv4.conf.default.rp_filter=0
net.ipv4.conf.all.rp_filter=0

net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.all.arp_announce=2
net.ipv4.conf.lo.arp_announce=2
net.ipv4.conf.all.arp_ignore=1
net.ipv4.conf.lo.arp_ignore=1
net.bridge.bridge-nf-call-ip6tables = 0
net.bridge.bridge-nf-call-iptables = 0
net.bridge.bridge-nf-call-arptables = 0

# 关闭tcp链接传输的慢启动
net.ipv4.tcp_slow_start_after_idle = 0

# Linux实例NAT哈希表满导致ECS实例丢包
# nf_conntrack_buckets * 4 = nf_conntrack_max
# net.netfilter.nf_conntrack_buckets = 163837
net.netfilter.nf_conntrack_max = 655350
net.netfilter.nf_conntrack_tcp_timeout_established = 1200
```
## 阿里云系统（alibaba cloud linux 3）默认配置
```bash
• Alibaba cloud linux 3 default sysctl configure
vm.swappiness = 0
kernel.sysrq = 1

net.ipv4.neigh.default.gc_stale_time = 120

# see details in <https://help.aliyun.com/knowledge_detail/39428.html>
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.default.rp_filter = 0
net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.lo.arp_announce = 2
net.ipv4.conf.all.arp_announce = 2

# see details in <https://help.aliyun.com/knowledge_detail/41334.html>
net.ipv4.tcp_max_tw_buckets = 262144
net.ipv4.tcp_syncookies = 1

# tcp_max_syn_backlog will only take effect when net.ipv4.tcp_syncookies == 0
# net.ipv4.tcp_max_syn_backlog = 65536
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_slow_start_after_idle = 0
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/sysctl/  

