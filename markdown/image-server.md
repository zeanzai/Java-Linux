

## 方式一

这种方式是指ng直接作为图片服务器，不提供上传和下载的功能。配置方式如下：

1. 创建文件夹，用来存放图片地址

2. 配置nginx

3. 测试地址

4. 开放端口

```
firewall-cmd --zone=public --permanent --add-port=8103/tcp
firewall-cmd --reload
```


## 方式二

这种方式是使用ftp作为上传下载的工具，ng作为代理的工具的配置方式。配置过程如下：


### 安装vsftpd

```shell
$ yum install -y vsftpd
```

### 添加用户并设置密码

```shell
$ useradd ftpuser
$ passwd ftpuser
```

### 开放21端口

```shell
$ firewall-cmd --zone=public --add-port=21/tcp --permanent
$ firewall-cmd --reload
```

### 配置selinux

```shell
$ getsebool -a | grep ftp
ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> off
httpd_can_connect_ftp --> off
httpd_enable_ftp_server --> off
tftp_anon_write --> off
tftp_home_dir --> off

$ setsebool -P allow_ftpd_full_access on
$ setsebool -P tftp_home_dir on
```

### 修改vsftpd.conf

/etc/vsftpd/vsftpd.conf YES->NO

```shell
anonymous_enable=NO
```

### 开机自起

```shell
$ systemctl enable vsftpd
```

### 配置图片根目录

```shell
$ cd /home/ftpuser/
$ mkdir /home/ftpuser/www
$ mkdir /home/ftpuser/www/images
$ chmod -R 777 /home/ftpuser
```

### 配置ng

```shell
server {
        listen       80;
        server_name  image.beidiancloud.com;

        location / {
            root   /home/ftpuser/www;
            index  index.html index.htm;
        }
    }
```

### 配置host文件

file loaction: C:\Windows\System32\drivers\etc

`192.168.100.203 image.beidiancloud.com`

### 测试

use filezilla to upload some image to `/home/ftpuser/www/images`

succeed!


### 备注

```shell
// list the port opened
$ firewall-cmd --list-ports

// open 80 port
$ firewall-cmd --zone=public(作用域) --add-port=80/tcp(端口和访问类型) --permanent(永久生效)

// reload firewall
$ firewall-cmd --reload

// stop firewall
$ systemctl stop firewalld.service

// disable firewalld autorun after boot operate system
$ systemctl disable firewalld.service

// enable firewall autorun after boot operate system
$ systemctl enable firewalld.service

// delete some port
$ firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

### 参考

> 1. CentOS7 安装vsftpd 服务器 https://blog.csdn.net/uq_jin/article/details/51684722<br />
> 2. Centos7,配置防火墙，开启端口 https://blog.csdn.net/u013410747/article/details/61696178<br />
> 3. SELinux百度百科： https://baike.baidu.com/item/SELinux/8865268?fr=aladdin<br />
> 4. 【Linux/CentOS】Boolean ftp_home_dir is not defined https://www.cnblogs.com/guxin/p/centos-boolean-ftp-home-dir-is-not-defined.html<br />
