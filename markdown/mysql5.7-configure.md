# 场景分析
在使用Navicat工具软件创建数据库表时，会把创建的数据库表的字符集默认为拉丁文。使用命令查询：
`show variables like 'character_set%';`
发现有一项是拉丁文。

# 解决方案

```
// 在配置文件中设置默认字符集
$ vi /etc/my.cnf

// 在[mysqld]下添加下面三行
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci

// 重启
$ systemctl restart mysqld
```
这样创建数据库时，就会使用默认的字符集了。

-----

-----

# 场景分析
数据库动不动就显示连接数量太多的问题。
执行：
```
show variables like '%max_connections%'
```
发现连接数为：151

# 解决方案
```
// 在配置文件中设置默认字符集
$ vi /etc/my.cnf

// 在[mysqld]下添加下面一行
max_connections=1000

// 重启
$ systemctl restart mysqld
```
执行：
```
show variables like '%max_connections%'
```
发现连接数为：1000
