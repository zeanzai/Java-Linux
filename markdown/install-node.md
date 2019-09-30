# 前言

node是部署前端应用的重要工具，它的地位类似jdk之于java程序。

# 信息统计

- 下载地址：http://nginx.org/en/download.html
- 软件版本：nginx-1.14.1
- 安装地址：/usr/setup/nginx-1.14.1
- 配置文件地址：/usr/setup/nginx-1.14.1/conf/nginx.conf
- 占用端口：详见配置文件

# 安装

- 把下载好的文件上传到服务器

- 解压到安装目录

```
tar xvf node-v10.14.2-linux-x64.tar.xz -C /usr/setup/
```

- 进入解压后的文件夹，并拷贝文件目录

```
cd /usr/setup/node-v10.14.2-linux-x64/
```

- 配置环境变量

```
vi /etc/profile

// 添加一行，并在path变量后面添加 $NODE_HOME/bin
export NODE_HOME=/usr/setup/node-v10.14.2-linux-x64
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin:$NODE_HOME/bin
```

- 测试

```
[root@home node-v10.14.2-linux-x64]# node -v
v10.14.2
```
