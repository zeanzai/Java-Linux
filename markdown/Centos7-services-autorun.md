


# 1. Nginx服务自启动

## 1. 编写服务文件

vi /usr/lib/systemd/system/nginx.service
注意修改安装路径

```
[Unit]
Description=nginx - high performance web server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/softwares/nginx-1.12.2/sbin/nginx -c /softwares/nginx-1.12.2/conf/nginx.conf
ExecReload=/softwares/nginx-1.12.2/sbin/nginx -s reload
ExecStop=/softwares/nginx-1.12.2/sbin/nginx -s stop

[Install]
WantedBy=multi-user.target
```

## 2. 赋予754权限

```shell
[root@200 system]# chmod 754 /usr/lib/systemd/system/nginx.service
```

## 3. 设置开机自启动

```shell
[root@200 system]# systemctl enable nginx.service
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
[root@200 system]# systemctl enable nginx.service
```

## 4. 其他命令

```shell
systemctl [start | enable | disable | status | restart | stop] nginx.service
```

# 2. 其他软件开机自启动

## 1. 编写服务文件

### 1. php-fpm

vi /usr/lib/systemd/system/php-fpm.service

```shell
[Unit]
Description=php
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/sbin/php-fpm

[Install]
WantedBy=multi-user.target
```

### 2. redis

vi /usr/lib/systemd/system/redis.service

```shell
[Unit]
Description=Redis
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/softwares/redis-4.0.6/bin/redis-server /softwares/redis-4.0.6/bin/redis.conf
ExecStop=kill -INT 'cat /var/run/redis_6379.pid'
[Install]
WantedBy=multi-user.target
```

### 3. tomcat7

#### 1. tomcat7/bin 下面新建setenv.sh配置

```
#add tomcat pid
CATALINA_PID="$CATALINA_BASE/tomcat.pid"
#add java opts
JAVA_OPTS="-server-XX:PermSize=256M -XX:MaxPermSize=1024m -Xms512M -Xmx1024M-XX:MaxNewSize=256m"
```

#### 2. 修改bin/setclasspath.sh

export  JAVA_HOME=/softwares/jdk1.8.0_111

export  JRE_HOME=/softwares/jdk1.8.0_111/jre

#### 3. 新建service文件

vi /usr/lib/systemd/system/tomcat7.service

```shell
[Unit]
Description=Tomcat
After=syslog.targetnetwork.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/softwares/apache-tomcat-7.0.70/bin/tomcat.pid
ExecStart=/softwares/apache-tomcat-7.0.70/bin/startup.sh
ExecStop=kill -INT 'cat /softwares/apache-tomcat-7.0.70/bin/tomcat.pid'
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

## 2. 赋予权限

## 3. 开启自动重启

systemctl enable XXX.service

## 4. 测试

测试时需要保证软件已经处于关闭状态。



参考：
http://blog.csdn.net/tiantang_1986/article/details/53704966
http://blog.csdn.net/fresh123456/article/details/50925038
http://blog.csdn.net/chenxiabinffff/article/details/51374635
