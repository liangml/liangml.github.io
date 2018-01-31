* Linux系统往往有添加磁盘不够的情况，这时就需要添加新的硬盘。一般情况下需要重启服务器，这里我们来使用线刷方式读取Linux新增硬盘**

* 1.添加磁盘后fdisk -l磁盘没有显示**

![](assets/001/20180131-688bf07a.png)
* 2.查看主机总线号**

![](assets/001/20180131-0b4cd70e.png)  
* 3.重新扫描scsi总线来添加设备**
```
[root@localhost ~]# echo "- - -" > /sys/class/scsi_host/host0/scan
[root@localhost ~]# echo "- - -" > /sys/class/scsi_host/host1/scan
[root@localhost ~]# echo "- - -" > /sys/class/scsi_host/host2/scan
```
* 4.再次查看硬盘已经显示**

![](assets/001/20180131-43556781.png)
