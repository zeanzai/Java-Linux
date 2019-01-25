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

---

---

# 场景复现
“小王，数据字典我已经更新到svn了，你抽空把数据库表给创建一下哈。”“好勒，我忙完手头上的事儿立马创建……”
领导撂下一句话，我奋斗一上午。
打开数据字典，美滋滋的开始创建数据库表。可是打开Navicat创建的时候，**发现添加列时每一列的编码都是拉丁编码。**我菊花一紧，知道这是个大问题，如果不处理，哪个小弟手一抖，又要忙很久排查问题了，我可不允许这样的事情出现。说干就干，我打开百度，一顿操作……去你妹的，谷歌吧。几分钟后，找到解决方案。

# 解决过程
## 修改配置文件
```
[root@dev ~]# vi /etc/my.cnf

[client]
default-character-set=utf8

[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci

[mysql]
no-auto-rehash
default-character-set=utf8
```


## 重启mysqld
```
[root@dev ~]# systemctl restart mysqld
[root@dev ~]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since 五 2019-01-25 16:19:07 CST; 4s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 41124 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 41106 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 41127 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─41127 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

1月 25 16:19:06 dev.northmeter systemd[1]: Starting MySQL Server...
1月 25 16:19:07 dev.northmeter systemd[1]: Started MySQL Server.
```

## 测试
使用Navicat在创建的表格中添加一列。完美！


## 备注
如果在添加一列时，发现还是不行，可以先进行创建表的操作，然后再在创建的表格上面添加列。
```
CREATE DATABASE ${tablename} DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

# 参考链接
https://www.jianshu.com/p/90f751ea37d1
