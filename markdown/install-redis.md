# 前言
redis，我们常用来做缓存，也是著名的nosql存储中间件。

# 信息统计
下载地址：暂无。
软件版本： redis-3.0.0
安装地址： /usr/setup/redis-3.0.0
配置文档地址： /usr/setup/redis-3.0.0/redis.conf
占用端口：6379
用户名和密码：无

# 安装

- 安装gcc编译工具包
```
$ yum update -y
$ yum install -y gcc
```
- 解压
```
$ tar zxf /opt/package/redis-3.0.0.tar.gz -C /opt/unziped/
```
- 创建文件夹
```
$ mkdir /usr/setup/redis-3.0.0
$ mkdir /usr/setup/redis-3.0.0/log
$ mkdir /usr/setup/redis-3.0.0/data
```
- 进入解压后的redis目录并执行make命令
```
$ make
```
- 执行安装命令
```
$ make install PREFIX=/usr/setup/redis-3.0.0
```
- 拷贝配置文件
```
$ cp /opt/unziped/redis-3.0.0/redis.conf /usr/setup/redis-3.0.0
```
- 打开守护进程运行模式
```
// 修改配置文件，将daemonize的值改为yes
$ vi /usr/setup/redis-3.0.0/redis.conf
daemonize yes
```
- 加入开机自启
```
$ ./opt/unziped/redis-3.0.0/utils/install_server.sh
Welcome to the redis service installer
This script will help you easily set up a running redis server

Please select the redis port for this instance: [6379]
Selecting default: 6379
Please select the redis config file name [/etc/redis/6379.conf] /usr/setup/redis-3.0.0/redis.conf
Please select the redis log file name [/var/log/redis_6379.log] /usr/setup/redis-3.0.0/log/redis.log
Please select the data directory for this instance [/var/lib/redis/6379] /usr/setup/redis-3.0.0/data/6379
Please select the redis executable path [] /usr/setup/redis-3.0.0/bin/redis-server
Selected config:
Port           : 6379
Config file    : /usr/setup/redis-3.0.0/redis.conf
Log file       : /usr/setup/redis-3.0.0/log/redis.log
Data dir       : /usr/setup/redis-3.0.0/data/6379
Executable     : /usr/setup/redis-3.0.0/bin/redis-server
Cli Executable : /usr/setup/redis-3.0.0/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort.
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
```
- 开启端口，并重启防火墙
```
$ firewall-cmd --zone=public --permanent --add-port=6379/tcp
$ firewall-cmd --reload
```

- 重启后测试

```
1. 本机连接测试
$ cd /usr/setup/redis-3.0.0/bin/
$ ./redis-cli
127.0.0.1:6379> info

2. 开发机连接测试
cmd: redis-cli.exe -h 10.168.0.120 -p 6379
```

# 简单使用
```
./redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set a 10
OK
127.0.0.1:6379> get a
"10"
127.0.0.1:6379> incr a
(integer) 11
127.0.0.1:6379> get a
"11"
127.0.0.1:6379> decr a
(integer) 10
127.0.0.1:6379> keys *
1) "a"
127.0.0.1:6379> del a
(integer) 1
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> exit // 关闭客户端

```

# 参考链接
1. https://www.cnblogs.com/zhang-ke/p/5981108.html
2. https://blog.csdn.net/sjhuangx/article/details/79633112
3. https://redis.io/
4. http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html
5. https://blog.csdn.net/amandalm/article/details/79516745
