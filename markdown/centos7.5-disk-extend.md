## 场景描述
早上起来使用jenkins自动化部署时，发现日志中提示磁盘已满的问题，在查询解决此问题的过程中，有很多博客都建议修改构件保存天数。笔者在自动化部署的设置过程中，本身设置的保存天数就很小。然后查看一下磁盘的使用情况，发现root分区已使用100%。只能使用修改分区的方式进行修改了。
在此问题出现的头一天晚上，重启了一下服务器，然后rabbitmq就再也启动不起来了，查看日志文件，网上也说是因为磁盘已满的问题。
早上过来发现，showdoc也挂掉了，但是挂掉一段时间之后自动又好了。

## 解决过程

### 查看磁盘使用情况
```
[root@dev ~]# df -h
文件系统                     容量  已用  可用 已用% 挂载点
/dev/mapper/centos_dev-root  118G  118G   20K  100% /
devtmpfs                      32G     0   32G    0% /dev
tmpfs                         32G     0   32G    0% /dev/shm
tmpfs                         32G  9.8M   32G    1% /run
tmpfs                         32G     0   32G    0% /sys/fs/cgroup
/dev/sda1                   1014M  186M  829M   19% /boot
/dev/mapper/centos_dev-home  7.2T   26G  7.2T    1% /home
tmpfs                        6.3G     0  6.3G    0% /run/user/0
```

### 备份home
```
[root@dev ~]# cp -r /home/ /dev/homebak
```

### 卸载home
```
[root@dev ~]# umount /home
```

### 删除home所在的lv
```
[root@dev ~]# lvremove -y /dev/mapper/centos_dev-home
  Logical volume "home" successfully removed
```

### 显示lvm卷组信息
```
[root@dev ~]# vgdisplay
  --- Volume group ---
  VG Name               centos_dev
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               7.27 TiB
  PE Size               4.00 MiB
  Total PE              1907041
  Alloc PE / Size       38240 / <149.38 GiB
  Free  PE / Size       1868801 / <7.13 TiB
  VG UUID               js7DO7-igaq-Si7p-lAef-PkXA-FS3W-5hnfSu
```

### 扩展root所在的lv
```
[root@dev ~]# lvextend -L +3T /dev/mapper/centos_dev-root
  Size of logical volume centos_dev/root changed from 118.00 GiB (30208 extents) to <3.12 TiB (816640 extents).
  Logical volume centos_dev/root successfully resized.
```

### 扩展root文件系统
```
[root@dev ~]# xfs_growfs /dev/mapper/centos_dev-root
meta-data=/dev/mapper/centos_dev-root isize=512    agcount=4, agsize=7733248 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=30932992, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=15104, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 30932992 to 836239360
```

### 重新创建home的lv
```
[root@dev ~]# lvcreate -L 4T -n home centos_dev
  Logical volume "home" created.
```

### 创建home文件系统
```
[root@dev centos_dev]# mkfs.xfs /dev/centos_dev/home
meta-data=/dev/centos_dev/home   isize=512    agcount=4, agsize=268435455 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=1073741820, imaxpct=5
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=521728, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

### 挂载home并恢复备份
```
[root@dev centos_dev]# mount /dev/centos_dev/home /home
[root@dev centos_dev]# cp -r /dev/homebak/ /home/
[root@dev ~]# df -h
文件系统                     容量  已用  可用 已用% 挂载点
/dev/mapper/centos_dev-root  3.2T  118G  3.1T    4% /
devtmpfs                      32G   26G  6.3G   81% /dev
tmpfs                         32G     0   32G    0% /dev/shm
tmpfs                         32G  9.8M   32G    1% /run
tmpfs                         32G     0   32G    0% /sys/fs/cgroup
/dev/sda1                   1014M  186M  829M   19% /boot
tmpfs                        6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos_dev-home  4.0T   29G  4.0T    1% /home
```

### 参考链接
1. https://blog.csdn.net/huqigang/article/details/79710201
