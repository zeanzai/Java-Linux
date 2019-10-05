## 前言

centos7是最小化安装。安装在vm虚拟机上面。

## 信息统计

- 下载地址：https://dev.mysql.com/downloads/mysql/5.7.html#downloads
- 软件版本：mysql-5.7.24
- 安装地址：默认地址，即分散安装到很多目录
- 配置文件地址：/etc/my.cnf
- 日志文档地址：见配置文件
- 占用端口：3306

## 安装

- 检查安装环境

```shell
[root@home ~]# rpm -qa|grep mariadb
mariadb-libs-5.5.56-2.el7.x86_64
[root@home ~]# rpm -e mariadb-libs-* --nodeps
[root@home ~]# rpm -qa|grep mariadb
```

- 将下载好的mysql安装包上传到/opt/packages/目录下

- 解压

```shell
[root@home ~]# cd /opt/packages/
[root@home packages]# mkdir mysql5.7.24
[root@home packages]# tar xvf mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar -C mysql5.7.24/
mysql-community-common-5.7.24-1.el7.x86_64.rpm
mysql-community-minimal-debuginfo-5.7.24-1.el7.x86_64.rpm
mysql-community-embedded-compat-5.7.24-1.el7.x86_64.rpm
mysql-community-embedded-devel-5.7.24-1.el7.x86_64.rpm
mysql-community-embedded-5.7.24-1.el7.x86_64.rpm
mysql-community-libs-5.7.24-1.el7.x86_64.rpm
mysql-community-devel-5.7.24-1.el7.x86_64.rpm
mysql-community-server-5.7.24-1.el7.x86_64.rpm
mysql-community-libs-compat-5.7.24-1.el7.x86_64.rpm
mysql-community-client-5.7.24-1.el7.x86_64.rpm
mysql-community-server-minimal-5.7.24-1.el7.x86_64.rpm
mysql-community-test-5.7.24-1.el7.x86_64.rpm
[root@home packages]# ll mysql5.7.24/
总用量 585024
-rw-r--r--. 1 7155 31415  25398288 10月  5 2018 mysql-community-client-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415    280896 10月  5 2018 mysql-community-common-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   3840412 10月  5 2018 mysql-community-devel-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415  46773880 10月  5 2018 mysql-community-embedded-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415  24078240 10月  5 2018 mysql-community-embedded-compat-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415 130082964 10月  5 2018 mysql-community-embedded-devel-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   2272368 10月  5 2018 mysql-community-libs-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   2116016 10月  5 2018 mysql-community-libs-compat-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415  56029544 10月  5 2018 mysql-community-minimal-debuginfo-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415 173096508 10月  5 2018 mysql-community-server-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415  15167212 10月  5 2018 mysql-community-server-minimal-5.7.24-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415 119909528 10月  5 2018 mysql-community-test-5.7.24-1.el7.x86_64.rpm
```

- 安装

```shell
[root@home packages]# cd mysql5.7.24/
[root@home mysql5.7.24]# rpm -ivh mysql-community-common-5.7.24-1.el7.x86_64.rpm
[root@home mysql5.7.24]# rpm -ivh mysql-community-libs-5.7.24-1.el7.x86_64.rpm
[root@home mysql5.7.24]# rpm -ivh mysql-community-client-5.7.24-1.el7.x86_64.rpm
[root@home mysql5.7.24]# yum install -y net-tools perl
[root@home mysql5.7.24]# rpm -ivh mysql-community-server-5.7.24-1.el7.x86_64.rpm
```

- 初始化

```shell
[root@home mysql5.7.24]# mysqld --initialize --user=mysql
[root@home mysql5.7.24]# cat /var/log/mysqld.log
[root@home mysql5.7.24]# systemctl status mysqld
[root@home mysql5.7.24]# systemctl start mysqld
[root@home mysql5.7.24]# systemctl status mysqld
```

- 分配权限
  - 为root用户设置新密码：`root@2019&*(`
  - 创建只具有增删改查的远程登录用户test：`test!@#`
  - 创建具有全部权限的admin用户：`admin@2019!@#`
  - 【optional】为root用户设置远程登录权限

```shell
// 使用root登录
[root@home mysql5.7.24]# mysql -uroot -p
// 修改root密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root@2019&*(';
// 创建只具有增删改查的远程登录用户test
mysql> create user 'test'@'%' identified by 'test!@#';
mysql> grant select, insert, update, delete on *.* to 'test'@'%';
mysql> flush privileges;
// 创建具有全部权限的admin用户
mysql> create user 'admin'@'%' identified by 'admin@2019!@#';
mysql> grant all on *.* to 'admin'@'%' identified by 'admin@2019!@#';
mysql> flush privileges;
// 设置root远程登录权限【未执行】
mysql> grant all on *.* to 'root'@'%' identified by 'root@2019&*(';
mysql> flush privileges;
```

- 关闭服务

```shell
[root@home mysql5.7.24]# systemctl status mysqld
[root@home mysql5.7.24]# systemctl stop mysqld
```

- 修改配置文件

具体可以查看[mysql5.7配置文件详解](mysql/00.mysql5.7-config-file.md)。

```shell
[root@dev ~]# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
# 设置表名不区分大小写
lower_case_table_names=1
# 修改默认值，以达到5.6与5.7兼容的目的
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
# 数据库默认字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节）
character-set-server=utf8mb4
# 数据库字符集对应一些排序等规则，注意要和character-set-server对应
collation-server=utf8mb4_general_ci
# 设置默认存储引擎
default-storage-engine=INNODB
# 设置最大连接数
max_connections=1000
# 设置client连接mysql时的字符集,防止乱码
init_connect='SET NAMES utf8mb4'
#TIMESTAMP如果没有显示声明NOT NULL，允许NULL值
explicit_defaults_for_timestamp = true
```

- 启动服务
```shell
[root@home mysql5.7.24]# systemctl start mysqld
```

- 开放端口
```shell
[root@home mysql5.7.24]# firewall-cmd --zone=public --add-port=3306/tcp --permanent
success
[root@home mysql5.7.24]# firewall-cmd --reload
success
```

- 删除安装包

```shell
[root@home mysql5.7.24]# cd ..
[root@home packages]# rm -rf ./*
```

- 测试

略。


> 注意事项：
> 1. mysqld是mysql5.7的服务器进程，在初始化之前是不能启动的。
> 2. 进行配置文件中的设置时，可以根据公司各自需求进行自定义设置。
> 3. 安装完成之后要删除安装包。
> 4. utf8mb4字符集
> > 1. MySQL中的utf-8并不是真正意义上的utf-8,它只能存储1~3个字节长度的utf-8编码，而存储4个字节的必须用utf8mb4(mysql>=5.5.3支持)，否则会出现乱码。例如在微信管理系统中，消息文本使用了emoji表情:符号，必须使用utf8mb4进行储存。
>
>  > 2.注意最大字符长度：以INNODB为基础，utf8最长VARCHAR(255)，utf8mb4最长为VARCHAR(191)。
> > 3. 要求在的mysql>=5.5.3版本，表、字段必须使用utf8mb4字符集和utf8mb4整理。
