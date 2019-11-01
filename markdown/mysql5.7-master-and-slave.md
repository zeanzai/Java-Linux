

## 主从配置

### 规划

- MySQL版本：5.7.24
- master主机IP：192.168.100.220
- slave01主机IP：192.168.100.221


### 安装

#### 安装虚拟机

分别在不同的虚拟机上安装centos7.5。安装之后的信息如下：

```
mysql.master：
  网络信息：192.168.100.220
  用户名/密码：root root1003

mysql.slave01:
  网络信息：192.168.100.221
  用户名/密码：root root1003
```

#### 安装mysql

在三台虚拟机上面各自安装好mysql，并完成初始化。

- 为root用户设置新密码：`root@2019&*(`
- 创建只具有增删改查的远程登录用户test：`test!@#`
- 创建具有全部权限的admin用户：`admin@2019!@#`
- 【optional】为root用户设置远程登录权限

> 参考文档： https://zeanzai.me/Java-Linux/#/markdown/install-mysql


### 测试连通性

在master上面ping两台slave01的ip，保证能够ping通；分别在slave上ping master的ip，保证也能ping通。

## 主从复制配置

### 主机配置

1. 查看是否开启bin日志

```
[root@mysql ~]# ll /var/lib/mysql

// 未发现bin文件，表明未开启
```

2. 修改配置文件

```
vi /etc/my.cnf

#######################主从复制####################
server-id=1
log_bin=/var/lib/mysql/mysql-bin
```

3. 创建用户并授权

```
create user 'repl' identified by 'repl';
grant replication slave on *.* to 'repl'@'192.168.100.%' identified by 'repl';
flush privileges;
```

4. 重启服务

```
systemctl restart mysqld
```

5. 查看bin日志文件

```
[root@mysql ~]# ll /var/lib/mysql

// 发现存在bin文件，说明开启成功
```

### 从机配置

1. 修改配置文件

```
vi /etc/my.cnf

#######################主从复制####################
server-id=2
relay-log-index=slave-relay-bin.index
relay-log=slave-relay-bin

```

2. 重启服务

```
systemctl restart mysqld
```

### 建立主从关系

1. 主库登录mysql查看状态

```
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      154 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

2. 打开从库登录mysql，查看状态，进行如下配置

```
mysql> show slave status\G
Empty set (0.00 sec)

mysql> change master to master_host='192.168.100.220';
Query OK, 0 rows affected (0.01 sec)


mysql> change master to master_port=3306;
Query OK, 0 rows affected (0.01 sec)

mysql> change master to master_user='repl';
Query OK, 0 rows affected, 2 warnings (0.01 sec)

mysql> change master to master_password='repl';
Query OK, 0 rows affected, 2 warnings (0.01 sec)

mysql> change master to master_log_file='mysql-bin.000001';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> change master to master_log_pos=154;
Query OK, 0 rows affected (0.01 sec)

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 192.168.100.220
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 154
               Relay_Log_File: slave-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 154
              Relay_Log_Space: 154
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
                  Master_UUID:
             Master_Info_File: /var/lib/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State:
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
1 row in set (0.00 sec)

mysql> start slave;
Query OK, 0 rows affected (0.01 sec)

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.100.220
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 154
               Relay_Log_File: slave-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 154
              Relay_Log_Space: 527
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: d0a811e6-e804-11e9-99fe-000c29e1ed82
             Master_Info_File: /var/lib/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
1 row in set (0.00 sec)

```

> 注意：
> Slave_IO_Running: Yes
> Slave_SQL_Running: Yes
> 这两个参数要同时处于yes状态才行。

## 测试

1. 在主库上创建测试数据库，创建一张表，并插入一条测试数据

```
create database testrepl;

use testrepl;

create table staffs (
  id int primary key auto_increment,
  name varchar(24) not null default '' comment '姓名',
  age int not null default 0 comment '年龄',
  pos varchar(20) not null default '' comment '职位'
);

insert into staffs(name, age, pos) values('z3', 22, 'manager');

```

2. 在从库上查看是否有该数据库

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testrepl           |
+--------------------+
5 rows in set (0.00 sec)

mysql> use testrepl
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+--------------------+
| Tables_in_testrepl |
+--------------------+
| staffs             |
+--------------------+
1 row in set (0.00 sec)

mysql> select * from staffs;
+----+------+-----+---------+
| id | name | age | pos     |
+----+------+-----+---------+
|  1 | z3   |  22 | manager |
+----+------+-----+---------+
1 row in set (0.00 sec)

mysql>
```

## 参考

1. https://blog.csdn.net/qq_16676539/article/details/81906959
2. https://blog.csdn.net/alen_liu_sz/article/details/79226191
3. https://blog.csdn.net/alen_liu_sz/article/details/79451581
