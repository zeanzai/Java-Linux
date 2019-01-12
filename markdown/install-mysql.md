# 前言
略
# 信息统计
下载地址：https://dev.mysql.com/downloads/mysql/5.7.html#downloads
软件版本：mysql-5.7.24
安装地址：默认地址，即分散安装到很多目录
配置文件地址：/etc/my.cnf
日志文档地址：见配置文件
占用端口：3306

# 安装
- 检查安装环境
```shell
rpm -qa|grep mariadb
rpm -e mariadb-libs-* --nodeps
rpm -qa|grep mariadb
```

- 解压
```shell
tar xvf mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar
mkdir mysql5.7.24
mv mysql-community-* ./mysql5.7.24/
```

- 安装
```shell
cd mysql5.7.16/
rpm -ivh mysql-community-common-5.7.24-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.24-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.24-1.el7.x86_64.rpm
yum install -y net-tools perl
rpm -ivh mysql-community-server-5.7.24-1.el7.x86_64.rpm
```

- 初始化
```shell
mysqld --initialize --user=mysql
cat /var/log/mysqld.log
systemctl status mysqld
systemctl start mysqld
systemctl status mysqld
```

- 分配权限
```shell
// 使用root登录
mysql -uroot -p

// 修改root密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'northmeter@2017&*(';

// 创建能够在任意主机上登录的northmeter用户
create user 'northmeter'@'%' identified by 'northmeter!@#';

// 为northmeter用户赋予增删改查的权限
grant select, insert, update, delete on *.* to 'northmeter'@'%';
flush privileges;

// 创建管理员用户
create user 'admin'@'%' identified by 'admin@2017!@#';

// 赋予权限
grant all on *.* to 'admin'@'%' identified by 'admin@2017!@#';

// 刷新权限
flush privileges;

// 设置root远程登录权限【未执行】
grant all on *.* to 'root'@'%' identified by 'northmeter@2017&*(';
flush privileges;
```

- 修改配置文件
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

# 设置不区分大小写
lower_case_table_names=1
```

- 开放端口
```shell
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```
